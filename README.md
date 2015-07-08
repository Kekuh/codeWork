# codeWork
// Worker.cpp : Defines the entry point for the console application.
//

#include "stdafx.h"
#include <iostream>
#include <string>
#include <fstream> // работа с файлами
#include <list>
#include <memory>
#include <mutex>

using namespace std;
using namespace std;

//Абстрактный класс рабочий
class Worker
{
protected:	
	string Name;			//Фамилия рабочего
	double Advance;			//Аванс рабочего
	double Salary;			//Среднемесячная заработная плата рабочего
	int ID;					//Идентификационный номер
public:
	Worker(){};				//Конструктор по умолчанию
	Worker(string name, double advance, int id)//Конструктор
	{
		Name = name;
		Advance = advance;
		ID = id;
	};
	virtual ~Worker(){};//Виртуальный диструктор 
	virtual void calc() = 0;//Виртуальный метод по расчёту среднемесячной заработной платы
	virtual ostream& print(ostream& ost = cout) = 0;//Виртуальный метод вывода объекта класса на печать
	
	friend void PrintSortingWorker(list<shared_ptr<Worker>> *list);//Дружественная функция для сортировки контейнера
	friend void FilegWrite(list<shared_ptr<Worker>> *list);//Дружественная функция записи в файл 
	friend void FilegRead(list<shared_ptr<Worker>> *list);//Дружественная функция чтения из файла
	friend bool operator <(const Worker &x, const Worker &y);//Дружественная функция оператора <	
	friend bool operator >(const Worker &x, const Worker &y);//Дружественная функция >
	friend bool operator ==(const Worker &x, const Worker &y);//Дружественная функция ==
};
//Класс для повременщика
class salPerHour : public Worker
{
	//list<shared_ptr<Worker>> list;
public:
	salPerHour(){};
	virtual ~salPerHour(){};
	salPerHour(string name, double salary, int id)
	{
		Name = name;
		Advance = salary;
		ID = id;
	};
	void calc()override
	{
		this->Salary = Advance * 20.8 * 8;
	}
	ostream& print(ostream& ost = cout) override
	{
		return ost << "Индификационный номер:\t" << ID
			<< "\nФамилия:\t\t\t" << Name
			<< "\nЗарплата почасовая:\t " << Advance
			<< "\nЗарплата расчётная:\t" << Salary;
	}
};
//Класс для постоянного рабочего
class salaryFixed : public Worker
{
	//list<shared_ptr<Worker>> list;
public:
	salaryFixed(){};
	virtual ~salaryFixed(){};

	salaryFixed(string name, double salary, int id)
	{
		Name = name;
		Advance = salary;
		ID = id;
	};
	ostream& print(ostream& ost = cout) override
	{
		return ost << "Индификационный номер:\t" << ID
			<< "\nФамилия:\t\t\t" << Name
			<< "\nЗарплата начисленная:\t " << Advance
			<< "\nЗарплата расчётная:\t" << Salary;
	}
	void calc()override
	{
		this->Salary = Advance;
	}
};
//Дружественная функция оператора <
bool operator <(const Worker &x, const Worker &y)
{
	return (x.Salary < y.Salary);
}
//Дружественная функция >
bool operator >(const Worker &x, const Worker &y)
{
	return (x.Name < y.Name);
}
//Дружественная функция ==
bool operator ==(const Worker &x, const Worker &y)
{
	return (x.Salary == y.Salary);
}
//Дружественная функция записи в файл
void FilegWrite(list<shared_ptr<Worker>> *list)
{
	ofstream fout("Worker.txt", ios_base::out | ios_base::trunc);
	if (!fout.is_open())
	{
		cout << "Файл не может быть открыт или создан\n";
		return;
	}
	fout << "ID\tName\tSalary\tSalH" << endl;
	for (auto it = list->begin(); it != list->end(); ++it)
	{
		fout << (*it)->ID << "\t" << (*it)->Name << "\t"
			<< (*it)->Salary << "\t" << (*it)->Advance << endl;
	}
	fout.close();
}
//Дружественная функция чтения из файла
void FilegRead(list<shared_ptr<Worker>> *list)
{
	system("cls");
	ifstream fin("Worker.txt");
	int c = list->size()+1;
	int b = 4;//количеваство столбцов
	string **s = new string*[c];
	for (int i = 0; i < c; i++)
	{
		s[i] = new string[b];
	}
	if (!fin.is_open())
	{
		cout << "Файл не может быть открыт или создан\n";
		return;
	}
	for (int i = 0; i < c; i++)
	{
		for (int j = 0; j < b; j++)
		{
			fin >> s[i][j];
		}
	}
	fin.close();
	for (int i = 0; i < c; i++)
	{
		for (int j = 0; j < b; j++)
		{
			cout << s[i][j] << "\t";
		}
		cout << endl;
	}
	for (int i = 0; i < c; i++)
	{
		delete[]s[i];
	}
	cin.get();
	cin.get();
}
//Функция для добавления рабочего
void AddWorker(list<shared_ptr<Worker>> *list)
{
	int choise = 0;
	while (1)
	{
		string name;
		double salary;

		int id;
		system("cls");
		cout << "Добавить рабочих." << endl;
		cout << "---------------------------------" << endl;
		cout << "Добавить работников с фиксированной оплатой [1]" << endl;
		cout << "Добавить работников с почасовой оплатой [2]" << endl;
		cout << "---------------------------------" << endl;
		cout << "Выход [0]" << endl;
		cout << "---------------------------------" << endl;
		cin >> choise;
		if (cin.good() && choise >= 0 && choise <= 2)
		{
			switch (choise)
			{
			case 0: return;
			case 1: system("cls");
				cout << "Введите Фамилию:";
				cin >> name;
				cout << "Введите почасовую оплату:";
				cin >> salary;
				cout << "Введите инд номер:";
				cin >> id;
				list->emplace_back(new salPerHour(name, salary, id)); break;
			case 2:system("cls");
				cout << "Введите Фамилию:";
				cin >> name;
				cout << "Введите начисление:";
				cin >> salary;
				cout << "Введите инд номер:";
				cin >> id;
				list->emplace_back(new salaryFixed(name, salary, id)); break;
			default:break;
			}
		}
	}
}
//Дружественная функция для сортировки контейнера
void PrintSortingWorker(list<shared_ptr<Worker>> *list)
{
	if (list->size() == NULL)
	{
		system("cls");
		cout << "Контейнер пуст." << endl;
		cin.get();
	}
	else
	{
		int choise = 0;
		while (1)
		{
			int c = 0;
			system("cls");
			for (auto const& p : *list)
				p->calc();
			for (auto it = list->begin(); it != list->end(); ++it)
			{
				for (auto in = it; in != list->end(); ++in)
				{
					if ((*it)->Salary == (*in)->Salary)
					{
						c++;
					}
				}
			}
			cout << c;
			if (c == list->size())
			{
				for (auto it = list->begin(); it != list->end(); ++it)
				{
					for (auto in = it; in != list->end(); ++in)
					{
						if ((*it)->Salary < (*in)->Salary)
						{
							iter_swap(it, in);
						}
					}
				}
			}
			else
			{
				for (auto it = list->begin(); it != list->end(); ++it)
				{
					for (auto in = it; in != list->end(); ++in)
					{
						if ((*it)->Name > (*in)->Name)
						{
							iter_swap(it, in);
						}
					}
				}
			}

			int count = 0;
			cout << "  Пункт сортировки данных" << endl;
			cout << "Упорядочить всю последовательность работников по убыванию среднемесячного заработка." << endl;
			cout << "При совпадении зарплаты – упорядочивать данные по алфавиту по имени." << endl;
			cout << "---------------------------------------------------------------------------------" << endl;
			cout << "Вывести результат сортировки [1]" << endl;
			cout << "Вывести первые 5 имен работников из полученного списка [2]" << endl;
			cout << "Вывести последние 3 идентификатора работников из полученного списка [3]" << endl;
			cout << "---------------------------------" << endl;
			cout << "Выход [0]" << endl;
			cout << "---------------------------------" << endl;
			cin >> choise;
			if (cin.good() && choise >= 0 && choise <= 3)
			{
				switch (choise)
				{
				case 0: return; break;
				case 1:
					for (auto it = list->begin(); it != list->end(); ++it)
					{
						cout << "------------------------------------------" << endl;
						(*it)->print() << endl;
						cout << "------------------------------------------" << endl << endl;
					}; break;
				case 2: count = 0;
					cout << "------------------------------------------" << endl;
					for (auto it = list->begin(); it != list->end(); ++it)
					{
						if (count < 5)
							cout << "Фамилия: " << (*it)->Name << endl;
						else
						{
							break;
						}
						count++;
					}
					cout << "------------------------------------------" << endl;
					break;
				case 3:
					count = 0;
					cout << "------------------------------------------" << endl;
					for (auto it = --list->end(); it != list->begin(); --it)
					{
						if (count < 3)
							cout << "Идентификационный номер: " << (*it)->ID << endl;
						else
						{
							break;
						}count++;
					}
					cout << "------------------------------------------" << endl; break;
				default: break;

				}
			}
			else
			{
				system("cls");
				cout << "Некоректный выбор пункта меню, попробуйте снова." << endl;
				cin.clear();
				system("pause");
			}
			while (cin.get() != '\n')
				continue;
			cin.get();
		}
	}
}
//Функция записи/чтения файла
void FilegWorker(list<shared_ptr<Worker>> *list)
{
	if (list->size() == NULL)
	{
		system("cls");
		cout << "Контейнер пуст." << endl;
		cin.get();
	}
	else
	{
		int choise = 0;
		while (1)
		{
			system("cls");
			cout << "Работа с файлами." << endl;
			cout << "---------------------------------" << endl;
			cout << "Записать данный в файл [1]" << endl;
			cout << "Считать с файла данные [2]" << endl;
			cout << "---------------------------------" << endl;
			cout << "Выход [0]" << endl;
			cout << "---------------------------------" << endl;
			cin >> choise;
			if (cin.good() && choise >= 0 && choise <= 2)
			{
				switch (choise)
				{
				case 0: return;
				case 1:	FilegWrite(list); break;
				case 2: FilegRead(list); break;
				default:break;
				}
			}
			else
			{
				system("cls");
				cout << "Некоректный выбор пункта меню, попробуйте снова." << endl;
				cin.clear();
				system("pause");
			}
			while (cin.get() != '\n')
				continue;
		}
	}
}
//Осноное меню программы
void menu(list<shared_ptr<Worker>> *list)
{
	int choise = 0;
	while (1)
	{
		system("cls");
		cout << "Работа со списком рабочих." << endl;
		cout << "---------------------------------" << endl;
		cout << "Добавить работников в фирму [1]" << endl;
		cout << "Вывод на экран всех рабочих [2]" << endl;
		cout << "Вывод на экран списка рабочих после сортировки [3]" << endl;
		cout << "Создание/чтение файла [4]" << endl;
		cout << "---------------------------------" << endl;
		cout << "Выход [0]" << endl;
		cout << "---------------------------------" << endl;
		cin >> choise;
		if (cin.good() && choise >= 0 && choise <= 4)
		{
			switch (choise)
			{
			case 0: return; break;
			case 1: AddWorker(list); break;
			case 2:
				if (list->size() == NULL)
				{		
					system("cls");
					cout << "Контейнер пуст." << endl;
					cin.get();
				}
				else
				{
					for (auto const& p : *list)
						p->calc();
					for (auto const& p : *list)
						p->print() << endl;
					cin.get();
				}
				; break;
			case 3: PrintSortingWorker(list); break;
			case 4: FilegWorker(list); break;
			default:break;
			}
		}
		else
		{
			system("cls");
			cout << "Некоректный выбор пункта меню, попробуйте снова." << endl;
			cin.clear();
			system("pause");
		}
		while (cin.get() != '\n')
			continue;
	}
}
//Ядро программы
int main()
{
	setlocale(LC_ALL, "Russian");
	list<shared_ptr<Worker>> list;
	list.emplace_back(new salPerHour("Teryn", 50, 1));
	list.emplace_back(new salPerHour("Oijua", 42, 2));	
	list.emplace_back(new salPerHour("Selin", 38, 3));
	list.emplace_back(new salPerHour("Peryn", 88, 4));
	list.emplace_back(new salaryFixed("Hebru", 5210, 5));
	list.emplace_back(new salaryFixed("Tetyb", 5110, 6));
	list.emplace_back(new salaryFixed("Metyb", 2700, 7));
	list.emplace_back(new salaryFixed("Lois", 4000, 8));
	for (auto const& p : list)
		p->calc();
	menu(&list);
	system("pause");
	return 0;
}

