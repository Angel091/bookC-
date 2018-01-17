# bookC-
C++
#include <vector>
#include <deque>
#include <list>
#include <map>
#include <set>
#include <initializer_list>
#include <iterator>
#include <algorithm>
#include <iostream>
#include <string>
#include <time.h>

using namespace std;

struct User {
public:
	int id;

	User(int id) : id(id) {}
};

struct InstanceOfBook {
public:
	int bookID;
	User* borrower;
	InstanceOfBook(int bookID) : bookID(bookID), borrower(nullptr){}
	InstanceOfBook(int bookID, User* borrower) : bookID(bookID), borrower(borrower) {}
};

struct Book {
public:
	string nameOfBook;
	vector<InstanceOfBook> booksCopies;

	Book(string nameOfBook) : nameOfBook(nameOfBook) {}
	Book(string nameOfBook, initializer_list<InstanceOfBook> booksCopies) : nameOfBook(nameOfBook), booksCopies(booksCopies){}
};

bool operator< (const Book& k1, const Book& k2) {
	return (k1.nameOfBook < k2.nameOfBook);
}

struct NewBookLoan {
public:
	string nameOfBook;
	int copieId;
	User* borrower;

	NewBookLoan(string nameOfBook, int copieId, User* borrower) : nameOfBook(nameOfBook), copieId(copieId), borrower(borrower) {}
};


deque<User*> generateUsers(int count) {
	deque<User*> users;
	for (int i = 0; i < count; i++)
	{
		users.push_back(new User{ i });
	}
	return users;
}

deque<Book> generateLibrary(deque<User*> users) {
#define U(x) (users[(x-1)])
	deque<Book> library{
		{ "Havran",{ 10,{ 12, U(1) },{ 13, U(2) }, 14, 15 } },
		{ "Mor",{ 500, 501,{ 502, U(1) }, 503, 504 } },
		{ "Cernota",{ { 101, U(1) }, 102,{ 103, U(2) },{ 104, U(3) }, 105 } },
		{ "Bubaci",{ { 1000, U(1) } } }
	};
	return library;
}

vector<InstanceOfBook> avaibleCopies(deque<Book> library, string bookName) {
	vector<InstanceOfBook> avaibleCopies{};
	for (auto it = library.begin(); it != library.end(); it++) {
		if (it->nameOfBook == bookName) {
			for (auto it1 = it->booksCopies.begin(); it1 != it->booksCopies.end(); it1++) {
				if (it1->borrower == nullptr) {
					avaibleCopies.push_back(*it1);
				}
			}
		}
	}

	return avaibleCopies;
}

void vypisAvaibleCopies(vector<InstanceOfBook> avaibleCopies, string bookName) {
	cout << "Avaible copies of " << bookName <<" count("<<avaibleCopies.size()<<")"<< ": ";
	for (auto it = avaibleCopies.begin(); it != avaibleCopies.end(); it++) {
		if (it != avaibleCopies.end()-1) {
			cout << it->bookID << ", ";
		}
		else {
			cout << it->bookID << endl;
		}
	}
}

vector<string> getNamesOfBookByBorrowerId(deque<Book> library, int id) {
	vector<string> bookNames{};
	for (auto it = library.begin(); it != library.end(); it++) {
		for (auto it1 = it->booksCopies.begin(); it1 != it->booksCopies.end(); it1++) {
			if (it1->borrower != NULL) {
				if (it1->borrower->id == id) {
					bookNames.push_back(it->nameOfBook);
				}
			}
		}
	}
	return bookNames;
}

deque<Book> addSomeBookBeforeLastElement(deque<Book> library) {
	srand(time(0));
	vector<string> nameOfBook{ "Kater", "Vojana"};
	string book = nameOfBook[rand() % nameOfBook.size()];
	auto bookIT = library.insert(library.end() - 1, book);
	bookIT->booksCopies.assign({ 20, 21});
	return library;
}

Book theMostLoanBook(deque<Book> library) {
	Book theMostLoanBook {""};
	int countLoan = 0;
	for (auto it = library.begin(); it != library.end(); it++) {
		int countPom = 0;
		for (auto it1 = it->booksCopies.begin(); it1 != it->booksCopies.end(); it1++) {
			if (it1->borrower != nullptr)
				countPom++;
			if (countLoan < countPom) {
				theMostLoanBook = it->nameOfBook;
				countLoan += countPom;
			}
		}
	}
	return theMostLoanBook;
}

string getNameByCopyId(deque<Book> library, int id) {
	string name = " ";
	for (auto it = library.begin(); it != library.end(); it++) {
		for (auto it1 = it->booksCopies.begin(); it1 != it->booksCopies.end(); it1++) {
				if (it1->bookID == id) {
					name = it->nameOfBook;
				}
		}
	}
	if (name == " ") {
		name = "The book with this id doesnt exist.";
	}
	return name;
}

void addAndSort(deque<Book>& library) {
	srand(time(0));
	vector<string> nameOfBook{ "Mir", "Ziv" };
	string book = nameOfBook[rand() % nameOfBook.size()];
	library.push_back(Book{book});
	sort(library.begin(), library.end(), [](const Book& b1, const Book& b2) {return (b1.nameOfBook < b2.nameOfBook); });
}

void makeIndexesFromBooksName(deque<Book> library, map<char, list<Book>>& indexBook) {
	list<Book> listPom;
	for (auto it = library.begin(); it != library.end(); it++) {
		for (int i = 0; i < it->nameOfBook.length(); i++)
		{
			if (i == 0) {
				char index = it->nameOfBook.at(i);
				listPom.push_back(*it);
				indexBook.insert(make_pair(index, listPom));
			}
		}
	}
}

void writeIndexBook(map<char, list<Book>>& indexBook) {
	for (auto it = indexBook.begin(); it != indexBook.end(); it++) {
		cout << it->first << endl;
		for (auto it1 = it->second.begin(); it1 != it->second.end(); it1++) {
			if (it->first == it1->nameOfBook.at(0)) {
				cout << it1->nameOfBook << " (";
				for (auto it2 = it1->booksCopies.begin(); it2 != it1->booksCopies.end(); it2++) {
					if (it2->borrower != nullptr)
						cout << "*" << it2->bookID << " ";
					else
						cout << it2->bookID << " ";
				}
				cout << ")" << endl;
			}
		}
	}
}

void makeSetsAvaibleNotAvBooks(deque<Book> library, set<Book>& avaibleBook, set<Book>& notAvaibleBook) {
	for (auto it = library.begin(); it != library.end(); it++) {
		int countA = 0;
		for (auto it1 = it->booksCopies.begin(); it1 != it->booksCopies.end(); it1++) {
			if (it1->borrower == nullptr)
				countA++;
		}

		if (countA != 0) {
			avaibleBook.insert(*it);
		}
		else {
			notAvaibleBook.insert(*it);
		}
	}
}

string sendBookToUser(deque<Book> library, User* user) {
	srand(time(0));
	vector<string> books{};
	for (auto it = library.begin(); it != library.end(); it++) {
		for (auto it1 = it->booksCopies.begin(); it1 != it->booksCopies.end(); it1++) {
			if (it1->borrower != NULL) {
				if (it1->borrower != user) {
					books.push_back(it->nameOfBook);
				}
			}
		}
	}
	return books[rand() % books.size()];
}

int main() {
	deque<User*> users = generateUsers(10);
	deque<Book> library = generateLibrary(users);
	
	srand(time(0));
	vector<string> nameOfBook{ "Havran", "Mor", "Cernota", "Bubaci"};
	string randBookName = nameOfBook[rand() % nameOfBook.size()];
	vector<InstanceOfBook> avaibleCopieOfHavrana = avaibleCopies(library, randBookName);
	vypisAvaibleCopies(avaibleCopieOfHavrana, randBookName);
	
	cout << endl;

	int userId = 2;
	vector<string> nameOfBooksByUserWithId = getNamesOfBookByBorrowerId(library,userId);
	cout << "Name of books takes by user[" << userId << "]: ";
	for (auto it = nameOfBooksByUserWithId.begin(); it != nameOfBooksByUserWithId.end(); it++) {
		if (it != nameOfBooksByUserWithId.end() - 1) {
			cout << *it << ",";
		}
		else {
			cout << *it << ".\n";
		}
	}

	sort(library.begin(), library.end(), [](const Book& b1, const Book& b2) {return (b1.nameOfBook < b2.nameOfBook);});
	library = addSomeBookBeforeLastElement(library);

	cout << endl;

	cout << "The most loan book ever: " << theMostLoanBook(library).nameOfBook << endl;
	
	cout << endl;
	
	vector<NewBookLoan> newBookLoan{
		{ "Mor", 503, U(2) },
		{ "Havran", 14, U(3) }
	};

	int idBook = 102;
	cout <<"ID "<< idBook <<" name of book: " << getNameByCopyId(library, idBook) << endl;
	
	cout << endl;

	addAndSort(library);
	addAndSort(library);

	map<char, list<Book>> indexBook{};
	makeIndexesFromBooksName(library, indexBook);
	writeIndexBook(indexBook);
	
	cout << endl;

	set<Book> avaibleBook{};
	set<Book> notAvaibleBook{};
	makeSetsAvaibleNotAvBooks(library, avaibleBook, notAvaibleBook);
	

	cout << "Book name is: "<< sendBookToUser(library, users[1]) << endl;
	system("pause");
	return 0;
}





