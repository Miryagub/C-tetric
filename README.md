#include<iostream>
#include <ctime>
#include "windows.h"
#include <conio.h>
#include <chrono>
#include <cstdio>

using namespace std;



enum ConsoleColor { //rənglər
	Black = 0,
	Blue = 1,
	Green = 2,
	Cyan = 3,
	Red = 4,
	Magenta = 5,
	Brown = 6,
	LightGray = 7,
	DarkGray = 8,
	LightBlue = 9,
	LightGreen = 10,
	LightCyan = 11,
	LightRed = 12,
	LightMagenta = 13,
	Yellow = 14,
	White = 15
};



const int M = 24; // oyun hündürlüyü
const int N = 12; // oyun sahəsi genişliyi
bool endgame = 0;
bool endproc = 0;

int pole[N][M + 4] = { 0 }; //oyun sahəsi
// Taymer və gecikmə üçün dəyişənlər
float timer = 0, delay = 0.0005;

int x[4], y[4];
int x2[4], y2[4];

int kol, score;

// Rəqəmlər massivi
int figures[7][4] =
{
	1,3,5,7,
	2,4,5,7,
	3,5,4,6,
	3,5,4,7,
	2,3,5,7,
	3,5,7,6,
	2,3,4,5,
};

int dx, x3, y3, dy; // üfüqi / şaquli hərəkət və formanın göstərilməsi üçün dəyişən


class Timer // zamanlama üçün bir timer sinifinin yaradılması
{
private:
	// Tip taxma adlar iç içə olan növlərə rahat giriş üçün istifadə olunur
	using clock_t = std::chrono::high_resolution_clock;
	using second_t = std::chrono::duration<double, std::ratio<1> >;

	std::chrono::time_point<clock_t> m_beg;

public:
	Timer() : m_beg(clock_t::now())
	{
	}

	void reset()
	{
		m_beg = clock_t::now();
	}

	double elapsed() const
	{
		return std::chrono::duration_cast<second_t>(clock_t::now() - m_beg).count();
	}
};





int main()
{
	COORD position;										// Lazımi quruluşun elan edilməsi
	HANDLE hConsole = GetStdHandle(STD_OUTPUT_HANDLE);	// Standart çıxışı idarə etmək



	bool rotate = 0; // dönmək üçün dəyişən


	//sahə hüdudlarını təyin etmək
	for (int i = 0; i < M; i++) {
		pole[0][i] = 2;
		pole[N - 1][i] = 2;
	}
	for (int i = 0; i < N; i++) {
		pole[i][M - 1] = 2;
	}

	score = 0;


	srand(time(0)); // avtomatik randomizasiya
	int n2 = rand() % 8; //forma növünü təyin edin

	while (endgame == 0) //əsas tətbiq dövrü: oyun bitənə qədər işləyir
	{
		int n = n2;

		for (int i = 0; i < 4; i++)
		{
			// Formanın hər bir hissəsinin mövqeyinin təyin edilməsi
			x[i] = figures[n2][i] % 2 + 5;
			y[i] = figures[n2][i] / 2;

			pole[x[i]][y[i]] = 1;
		}

		srand(time(0)); //avtomatik randomizasiya
		n2 = rand() % 8; //rəqəmin növbəti fiqurunun növünü təyin edin


		//ikinci tətbiq dövrü: rəqəm düşərkən icra olunur
		while (endproc == 0)
		{
			//növbəti formanı çəkmək
			position.X = 47;	// X koordinatının qurulması
			position.Y = 5;	// Y koordinatının qurulması
			SetConsoleCursorPosition(hConsole, position);
			SetConsoleTextAttribute(hConsole, (WORD)((Black << 4) | LightGreen));
			cout << "Novbeti fiqur:";
			SetConsoleTextAttribute(hConsole, (WORD)((Black << 4) | White));

			for (int i = 0; i < 8; i++) // köhnə rəsmin çıxarılması
			{
				// Formanın hər bir hissəsinin mövqeyinin təyin edilməsi
				x3 = i % 2;
				y3 = i / 2;

				position.X = x3 * 2 + 52;	// X koordinatının qurulması
				position.Y = y3 + 7;	// Y koordinatının qurulması
				SetConsoleCursorPosition(hConsole, position);
				cout << "  ";
			}

			for (int i = 0; i < 4; i++) // növbəti rəqəmin çəkilməsi
			{
				// Formanın hər bir hissəsinin mövqeyinin təyin edilməsi
				x3 = figures[n2][i] % 2;
				y3 = figures[n2][i] / 2;

				position.X = x3 * 2 + 52;	//  X koordinatının qurulması
				position.Y = y3 + 7;	//  X koordinatının qurulması
				SetConsoleCursorPosition(hConsole, position);
				cout << " #";
			}

			delay = 0.0006;
			// Taymer başlayır
			Timer t;

			int key = 0;
			rotate = 0;

			if (_kbhit())
			{
				key = _getch();
			}

			if (key == 27) { endgame = 1; endproc = 1; }

			switch (key) //hərəkətlərin və fırlanmaların idarə olunması
			{
			case 72:
				rotate = 1;
				break;

			case 80:
				delay = 0.000005;
				break;

			case 77:
				dx = 1;
				for (int i = 0; i < 4; i++)
				{
					pole[x[i]][y[i]] = 0;
					if ((pole[x[i] + 1][y[i]] == 2) || (pole[x[i] + 1][y[i]] == 3)) dx = 0;

				}
				for (int i = 0; i < 4; i++)
				{
					x[i] = x[i] + dx;
					pole[x[i]][y[i]] = 1;
				}
				dx = 0;
				break;

			case 75:
				dx = -1;
				for (int i = 0; i < 4; i++)
				{
					pole[x[i]][y[i]] = 0;
					if ((pole[x[i] - 1][y[i]] == 2) || (pole[x[i] - 1][y[i]] == 3)) dx = 0;
				}
				for (int i = 0; i < 4; i++)
				{
					x[i] = x[i] + dx;
					pole[x[i]][y[i]] = 1;
				}
				dx = 0;
				break;

			case 32: // yer fasiləsi
				//Davam etmək üçün hər hansı birinə basın
				position.X = 5;	// X koordinatının qurulması
				position.Y = 23;	// Y koordinatının qurulması
				SetConsoleCursorPosition(hConsole, position);

				SetConsoleTextAttribute(hConsole, (WORD)((Black << 4) | LightMagenta));

				cout << "Dayandirdiniz. ";
				system("pause");

				// Gərəksiz yazının silinməsi
				position.X = 5;	// X koordinatının qurulması
				position.Y = 23;	//Y koordinatının qurulması
				SetConsoleCursorPosition(hConsole, position);
				cout << "                                                                   ";

				SetConsoleTextAttribute(hConsole, (WORD)((Black << 4) | White));
				break;
			}




			if (rotate)
			{
				// fırlanma mərkəzini göstərin
				int x0 = x[1];
				int y0 = y[1];

				int proverka = 0;

				for (int i = 0; i < 4; i++)
				{
					pole[x[i]][y[i]] = 0;
					x2[i] = x0 - (y[i] - y0);
					y2[i] = y0 + (x[i] - x0);

					if ((pole[x2[i]][y2[i]] == 3) || (pole[x2[i]][y2[i]] == 2))
						proverka = 1;
				}
				for (int i = 0; i < 4; i++)
				{
					if (proverka == 0) {
						x[i] = x2[i]; y[i] = y2[i];
					}
					pole[x[i]][y[i]] = 1;
				}
			}


			float time = t.elapsed();

			timer = timer + time;


			for (int i = 0; i < 4; i++)
			{
				if ((pole[x[i]][y[i] + 1] == 3) || (pole[x[i]][y[i] + 1] == 2))
					endproc = 1;
			}

			//  Aşağı hərəkət (taymer vaxtı ilə)
			if (timer > delay)
			{
				for (int i = 0; i < 4; i++)
				{
					pole[x[i]][y[i]] = 0;
					y[i]++;
				}
				for (int i = 0; i < 4; i++)
				{
					pole[x[i]][y[i]] = 1;
				}
				timer = 0;
			}


			for (int i = 4; i < M; i++) {

				position.X = 15;	// X koordinatının qurulması
				position.Y = i - 4;	// Y koordinatının qurulması
				SetConsoleCursorPosition(hConsole, position);

				for (int j = 0; j < N; j++) {

					if ((i == M - 1) && (j > 0))
					{
						SetConsoleTextAttribute(hConsole, (WORD)((Red << 4) | Blue));
						cout << "#";
						SetConsoleTextAttribute(hConsole, (WORD)((Black << 4) | White));
					}
					else cout << " ";


					if (pole[j][i] == 1) { cout << "#"; }
					if (pole[j][i] == 2)
					{
						SetConsoleTextAttribute(hConsole, (WORD)((Red << 4) | Blue));
						cout << "0";
						SetConsoleTextAttribute(hConsole, (WORD)((Black << 4) | White));
					}
					if (pole[j][i] == 0) { cout << " "; }
					if (pole[j][i] == 3) { cout << "#"; }
				}
			}


			position.X = 47;	//Xkoordinatının qurulması
			position.Y = 2;	// Y koordinatının qurulması
			SetConsoleCursorPosition(hConsole, position);
			SetConsoleTextAttribute(hConsole, (WORD)((Black << 4) | LightGreen));
			cout << "Skorunuz:  ";
			SetConsoleTextAttribute(hConsole, (WORD)((Black << 4) | LightCyan));
			cout << score;
			SetConsoleTextAttribute(hConsole, (WORD)((Black << 4) | LightGreen));

		}

		for (int i = 0; i < 4; i++) // dayandırılmış bir fiqur çəkmə proseduru
		{
			pole[x[i]][y[i]] = 3;
			endproc = 0;

			if (y[i] < 5) endgame = 1; // parçanın qutunun içərisində olub olmadığını yoxlamaq
		}

		//tamamlanmış xətt yoxlama proseduru

		int i, j, k, l = 0;
		for (i = 0; i < M - 1; i++)
		{
			k = 1;
			for (j = 1; j < N - 1; j++) // doldurulmuş xəttin yoxlanılması
				if (pole[j][i] == 0)
				{
					k = 0; break;
				}

			if (k == 1) // doldurulmuş sətri silin
			{
				kol++;
				for (l = i; l > 0; l--)
					for (j = 1; j < N - 1; j++)
						pole[j][l] = pole[j][l - 1];
			}


		}

		switch (kol) //hərəkətlərin və fırlanmaların idarə olunması
		{
		case 1:
			score = score + 100;
			break;

		case 2:
			score = score + 300;
			break;

		case 3:
			score = score + 700;
			break;

		case 4:
			score = score + 1500;
			break;
		}

		kol = 0;
	}

	// Oyundan çıxmaq üçün esc düyməsini basın
	position.X = 15;	// X koordinatının qurulması
	position.Y = 23;	// Y koordinatının qurulması  
	SetConsoleCursorPosition(hConsole, position);
	SetConsoleTextAttribute(hConsole, (WORD)((Black << 4) | LightRed));
	cout << "OYUN Sona catdi xais olunur: ESC duymesini basin" << endl;
	SetConsoleTextAttribute(hConsole, (WORD)((Black << 4) | White));

	while (endproc == 0)
	{
		int key;
		key = _getch();

		switch (key) //hərəkətlərin və fırlanmaların idarə olunması
		{
		case 27:
			endproc = 1;
			break;

		default:
			endproc = 0;
		}
	}
	return 0;
}

