# Matrix
Вычисление матриц
#include <iostream>
#include <iomanip>
#include "math.h"
#include <fstream>

using namespace std;

double f(int i, int j)
{
	return cos(i - j) / sin(i + j);// задаём функцию
}


double determinant(double** A, int n)// определитель матрицы
{
	double det = 1;
	for (int i = 0; i < n; i++)
	{
		det = det * A[i][i];
	}
	return det;
}


void Multiplication(double** R, double** B, double** C, int n)//функция произведения матриц
{
	double sum = 0;
	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < n; j++)
		{
			for (int k = 0; k < n; k++)
			{
				sum += R[i][k] * B[k][j];
			}
			C[i][j] = sum;
			sum = 0;
		}
	}
}


double NormaMatrix(double** R, int n)//вычисление нормы матрицы
{
	double max1 = 0, max2 = 0;
	for (int j = 0; j < n; j++)
	{
		for (int i = 0; i < n; i++)
		{
			max1 = max1 + abs(R[i][j]);
		}
		if (max1 > max2) max2 = max1;
		max1 = 0;
	}
	return max2;
}

void main()
{
	setlocale(LC_CTYPE, "ru");
	int n = 0;
	cout << "Размер матрицы n = ";
	cin >> n; // задаём размер матрицы

	double** R = new double* [n]; // расширенная матрица n x 2n, с единичной матрицей в правой части
	double** N = new double* [n]; // просто матрица размером n x n
	for (int i = 0; i < n; i++)
	{
		R[i] = new double[2 * n];
		N[i] = new double[n];
	}
	
	for (int i = 0; i < n; i++)// заполнение матриц R и N
	{
		for (int j = 0; j < n; j++)
		{
			R[i][j] = f(i + 1, j + 1);
			N[i][j] = R[i][j];
		}
		for (int j = n; j < 2 * n; j++)
		{
			if (i == j - n) R[i][j] = 1;
			else R[i][j] = 0;
		}
	}

	cout << endl;
	cout << "Расширенная матрица" << endl;
	cout << endl;

	for (int i = 0; i < n; i++)// вывод на экран расширенной матрицы R (состоит из матрицы N и единичной матрицы)
	{
		for (int j = 0; j < 2 * n; j++)
		{
			cout << setw(4) << setprecision(8) << R[i][j] << '\t';
		}
		cout << endl;
	}
	cout << endl;

	// прямой ход метода Гаусса

	double Rmax, C;
	int z;
	for (int k = 0; k <= n - 2; k++)
	{
		Rmax = abs(R[k][k]);
		z = k; // р - номер строки с максимальным по модулю элементом

		for (int i = k + 1; i <= n - 1; i++)// выбор строки с макс. по модулю эл-том в к-ом столбце среди к, к + 1, ..., n строк
		{
			if (abs(R[i][k] > Rmax))
			{
				Rmax = abs(R[i][k]);
				
				z= i;
			}
		}

		
		for (int j = k; j < 2 * n - 1; j++)// перестановка строк
		{
			C = R[k][j];
			R[k][j] = R[z][j];
			R[z][j] = C;
		}

		double s;
		for (int i = k + 1; i <= n - 1; i++)
		{
			s = R[i][k] / R[k][k];
			for (int j = k; j <= 2 * n - 1; j++)
			{
				R[i][j] = R[i][j] - R[k][j] * s;
			}
		}
	}

	determinant(R, n);
	cout << "Определитель матрицы = " << determinant(R, n) << endl << endl;

	// обратный ход метода Гаусса
	//создание обратной матрицы 
	double** B = new double* [n];
	for (int i = 0; i < n; i++)
	{
		B[i] = new double[n];
	}
	
	for (int k = 0; k <= n - 1; k++)// вычисление элементов обратной матрицы В
	{
		B[n - 1][k] = R[n - 1][n + k];
		for (int i = n - 1; i >= 0; i--)
		{
			double S = R[i][n + k];
			for (int j = n - 1; j >= i + 1; j--)
			{
				S = S - R[i][j] * B[j][k];
			}
			B[i][k] = S / R[i][i];
		}

	}

	
	for (int i = 0; i < n; i++)//вывод на экран обратной матрицы
	{ 	
		for (int j = 0; j < n; j++)
		{
			cout << setprecision(3) << setw(3)  << B[i][j] << '\t';
		}
		cout << endl;
	}
	cout << endl;
	cout << "Обратная матрица :" << endl;


	
	double** H = new double* [n];// H - произведение матриц 
	for (int i = 0; i < n; i++)
	{
		H[i] = new double[n];
	}

	Multiplication(N, B, H, n); //Произведение
	// вывод матрицы H на экран
	cout << endl;
	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < n; j++)
		{
			cout << setw(3) << H[i][j] << '\t';
		}
		cout << endl;
	}

	// число обусловленности
	cout << endl << "Число обусловленности = " << NormaMatrix(N, n) * NormaMatrix(B, n) << endl;


	// вывод в файл расширенной, обратной матрицы; матрицы произведения и числа обусловленности
	ofstream file;
	file.open("Matrix.txt");
	if (!file.is_open())
	{
		cout << "ERROR" << endl; // ошибка открытия файла
	}
	else
	{
		for (int i = 0; i < n; i++)
		{
			for (int j = 0; j < 2 * n; j++)
			{
				file << setw(8) << R[i][j] << '\t';
			}
			file << endl;
		}
		file << endl;

		file << "Определитель матрицы = " << determinant(R, n) << endl << endl;

		for (int i = 0; i < n; i++)
		{
			for (int j = 0; j < n; j++)
			{
				file << setw(8) << B[i][j] << '\t';
			}
			file << endl;
		}
		file << endl;


		for (int i = 0; i < n; i++)
		{
			for (int j = 0; j < n; j++)
			{
				file << setw(8) << H[i][j] << '\t';
			}
			file << endl;
		}
		file << endl;

		file << "Число обусловленности = " << NormaMatrix(N, n) * NormaMatrix(B, n) << endl;
	}
	file.close();



	for (int i = 0; i < 2 * n; i++)
	{
		delete[] R[i]; // очистка памяти расширенной матрицы
	}
	delete[] R;
	for (int i = 0; i < n; i++)
	{
		delete[] B[i]; // очистка памяти обратной матрицы
		delete[] H[i]; // очистка памяти матрицы произведения
	}
	delete[] B;
	delete[] H;
}
