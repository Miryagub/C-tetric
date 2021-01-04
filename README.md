 #include <iostream>
#include <ctime>
using namespace std;

int main()
{
	double num, num1;
	cout << "Ededleri daxil edin";
	cin >> num >> num1;
	cout << "Xais olunur eded daxil edin" << endl;
	cout << "1.Ededlerin toplanmasi"<< endl;
	cout << "2.Ededlerin hasili"<<endl;
	cout << "3.Ededlerin cixilmasi"<<endl;
	cout << "4.Ededlerin Vurulmasi"<<endl;
	int secim;
	cin >> secim;
	switch (secim)
	{
	case 1:
		cout << "1.Ededlerin toplanmasi" << num + num1;
		break;
	case 2:
		cout << "2.Ededlerin hasili" << num * num1;
		break;
	case 3:
		cout << "3.Ededlerin cixilmasi" << num - num1;
		break;
	case 4:
		cout << "4.Ededlerin Vurulmasi" << num / num1;
		break;

	}
}  
