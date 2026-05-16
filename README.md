#include <iostream>
#include <fstream>
#include <string>
#include <vector>
#include <algorithm>
#include <ctime>

using namespace std;

class Abiturient {
private:
    string surname;
    string name;
    string patronymic;
    string address;
    int balMath;
    int balRus;
    int balInf;

    static int objectCount;

public:
    Abiturient() {
        surname = "";
        name = "";
        patronymic = "";
        address = "";
        balMath = 0;
        balRus = 0;
        balInf = 0;
        objectCount++;
        cout << "[Конструктор по умолчанию] Создан объект. Всего объектов: " << objectCount << endl;
    }

    Abiturient(string sur, string n, string pat, string addr) {
        surname = sur;
        name = n;
        patronymic = pat;
        address = addr;
        balMath = 0;
        balRus = 0;
        balInf = 0;
        objectCount++;
        cout << "[Конструктор с параметрами] Создан объект: " << name << " " << surname << ". Всего объектов: " << objectCount << endl;
    }

    Abiturient(string sur, string n, string pat, string addr, int math, int rus, int inf) {
        surname = sur;
        name = n;
        patronymic = pat;
        address = addr;
        balMath = math;
        balRus = rus;
        balInf = inf;
        objectCount++;
        cout << "[Конструктор полный] Создан объект: " << name << " " << surname << ". Всего объектов: " << objectCount << endl;
    }

    Abiturient(const Abiturient& other) {
        surname = other.surname;
        name = other.name;
        patronymic = other.patronymic;
        address = other.address;
        balMath = other.balMath;
        balRus = other.balRus;
        balInf = other.balInf;
        objectCount++;
        cout << "[Конструктор копирования] Скопирован объект: " << name << " " << surname << ". Всего объектов: " << objectCount << endl;
    }

    ~Abiturient() {
        objectCount--;
        cout << "[Деструктор] Удалён объект: " << name << " " << surname << ". Осталось объектов: " << objectCount << endl;
    }

    static int getObjectCount() { return objectCount; }

    void setSurname(string s) { surname = s; }
    void setName(string n) { name = n; }
    void setPatronymic(string p) { patronymic = p; }
    void setAddress(string a) { address = a; }
    void setBalMath(int m) { balMath = m; }
    void setBalRus(int r) { balRus = r; }
    void setBalInf(int i) { balInf = i; }

    string getSurname() const { return surname; }
    string getName() const { return name; }
    string getPatronymic() const { return patronymic; }
    string getAddress() const { return address; }
    int getBalMath() const { return balMath; }
    int getBalRus() const { return balRus; }
    int getBalInf() const { return balInf; }

    int Get_Sum_Bal() const {
        return balMath + balRus + balInf;
    }

    void show() const {
        cout << "ФИО: " << surname << " " << name << " " << patronymic << endl;
        cout << "Адрес: " << address << endl;
        cout << "Баллы по математике: " << balMath << endl;
        cout << "Баллы по русскому: " << balRus << endl;
        cout << "Баллы по информатике: " << balInf << endl;
        cout << "Сумма баллов: " << Get_Sum_Bal() << endl;
        cout << endl;
    }

    void loadFromFile(ifstream& file) {
        string temp;
        int bal;
        file >> temp; setSurname(temp);
        file >> temp; setName(temp);
        file >> temp; setPatronymic(temp);
        file >> temp; setAddress(temp);
        file >> bal; setBalMath(bal);
        file >> bal; setBalRus(bal);
        file >> bal; setBalInf(bal);
    }
};

int Abiturient::objectCount = 0;

void modify_object(Abiturient& a, int math, int rus, int inf) {
    cout << "\n[modify_object] Изменяем баллы объекта: " << a.getName() << " " << a.getSurname() << endl;
    a.setBalMath(math);
    a.setBalRus(rus);
    a.setBalInf(inf);
    cout << "[modify_object] После изменения:" << endl;
    a.show();
}

void try_to_modify_object(Abiturient a, int math, int rus, int inf) {
    a.setBalMath(math);
    a.setBalRus(rus);
    a.setBalInf(inf);
    cout << "\n[try_to_modify_object] Копия (исходный объект не изменён):" << endl;
    a.show();
}

vector<Abiturient> loadFromFile(const string& filename) {
    vector<Abiturient> abiturients;
    ifstream file(filename);

    if (!file.is_open()) {
        cout << "Ошибка: не удалось открыть файл " << filename << endl;
        return abiturients;
    }

    Abiturient temp;
    while (!file.eof()) {
        temp.loadFromFile(file);
        if (file.fail()) break;
        abiturients.push_back(temp);
    }

    file.close();
    cout << "Загружено " << abiturients.size() << " абитуриентов." << endl;
    return abiturients;
}

void findBelowThreshold(const vector<Abiturient>& abiturients, int minMath, int minRus, int minInf) {
    cout << "\n----- Абитуриенты с баллами ниже проходного -----" << endl;
    cout << "Проходные баллы: математика = " << minMath << ", русский = " << minRus << ", информатика = " << minInf << endl;
    bool found = false;
    for (const auto& s : abiturients) {
        if (s.getBalMath() < minMath || s.getBalRus() < minRus || s.getBalInf() < minInf) {
            s.show();
            found = true;
        }
    }
    if (!found) cout << "Абитуриентов с баллами ниже проходного не найдено." << endl;
}

void findBySum(const vector<Abiturient>& abiturients, int minSum) {
    cout << "\n----- Абитуриенты с суммой баллов >= " << minSum << " -----" << endl;
    bool found = false;
    for (const auto& s : abiturients) {
        if (s.Get_Sum_Bal() >= minSum) {
            s.show();
            found = true;
        }
    }
    if (!found) cout << "Абитуриентов с такой суммой баллов не найдено." << endl;
}

void findTop5(vector<Abiturient>& abiturients) {
    cout << "\n----- Топ-5 абитуриентов по сумме баллов -----" << endl;
    if (abiturients.empty()) { cout << "Список пуст." << endl; return; }
    sort(abiturients.begin(), abiturients.end(), [](const Abiturient& a, const Abiturient& b) {
        return a.Get_Sum_Bal() > b.Get_Sum_Bal();
        });
    int count = min(5, (int)abiturients.size());
    for (int i = 0; i < count; i++) {
        cout << (i + 1) << ". ";
        abiturients[i].show();
    }
}

int main() {
    setlocale(LC_ALL, "rus");

    Abiturient a1;                                                    
    Abiturient a2("Иванов", "Иван", "Иванович", "Москва");               
    Abiturient a3("Петров", "Пётр", "Петрович", "Казань", 85, 78, 92);   

    cout << "\n--- Демонстрация modify_object и try_to_modify_object ---" << endl;
    cout << "Объект ДО вызовов:" << endl;
    a3.show();

    try_to_modify_object(a3, 10, 10, 10);

    cout << "Объект a3 ПОСЛЕ try_to_modify_object (не изменился):" << endl;
    a3.show();

    modify_object(a3, 99, 99, 99);

    cout << "Объект a3 ПОСЛЕ modify_object (изменился):" << endl;
    a3.show();

    cout << "Счётчик объектов перед загрузкой из файла: " << Abiturient::getObjectCount() << endl;

    {
        vector<Abiturient> abiturients = loadFromFile("D:/Программирование/2 семестр/Лаб раб №1/abiturients.txt");

        if (!abiturients.empty()) {
            cout << "\n----- Все абитуриенты -----" << endl;
            for (const auto& s : abiturients) s.show();

            findBelowThreshold(abiturients, 40, 40, 40);

            int minSum;
            cout << "\nВведите минимальную сумму баллов: ";
            cin >> minSum;
            findBySum(abiturients, minSum);

            findTop5(abiturients);
        }

        cout << "\n[Блок классического варианта завершён — вектор будет уничтожен]" << endl;
    } 

    cout << "\nСчётчик после уничтожения вектора: " << Abiturient::getObjectCount() << endl;

    Abiturient* d1 = new Abiturient();
    Abiturient* d2 = new Abiturient("Сидоров", "Сидор", "Сидорович", "Новосибирск");
    Abiturient* d3 = new Abiturient("Козлов", "Козьма", "Козьмич", "Самара", 70, 65, 88);

    cout << "\nСчётчик после создания динамических объектов: " << Abiturient::getObjectCount() << endl;

    cout << "\n--- Демонстрация функций на динамическом объекте ---" << endl;
    cout << "d3 ДО:" << endl;
    d3->show();

    try_to_modify_object(*d3, 5, 5, 5);
    cout << "d3 ПОСЛЕ try_to_modify_object:" << endl;
    d3->show();

    modify_object(*d3, 100, 100, 100);
    cout << "d3 ПОСЛЕ modify_object:" << endl;
    d3->show();

    int n = 3;
    Abiturient* dynArray = new Abiturient[n]; 
    dynArray[0].setName("Алексей"); dynArray[0].setSurname("Алексеев");
    dynArray[1].setName("Борис");   dynArray[1].setSurname("Борисов");
    dynArray[2].setName("Вадим");   dynArray[2].setSurname("Вадимов");

    cout << "\nСчётчик с динамическим массивом: " << Abiturient::getObjectCount() << endl;

    cout << "\n--- Удаляем все динамические объекты ---" << endl;
    delete d1;
    delete d2;
    delete d3;
    delete[] dynArray;

    cout << "\nСчётчик после удаления всех динамических объектов: " << Abiturient::getObjectCount() << endl;

    cout << "\n--- Программа завершается, статические объекты a1, a2, a3 удалятся автоматически ---" << endl;
    return 0;
}
