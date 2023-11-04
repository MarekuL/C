Gauss.h
#pragma once
#include <iostream>
#include <iomanip>
#include <cmath>
using namespace std;

void Out(float** a, int n, int m);
float** Create(int n, int m, float* a);
void SwapLines(int x, int x2, float** a);
float* FindX2(float** arr, int n, int m);
void OutAns(float* ans, int n);
float* ResidualVect(float* ans, float* arr, int m, int n);
float** CreateB(int n, int m, float* a, float* b);
void del(float* ans, float* ansb, int n);





Methods.cpp

#include "Gauss.h"
void del(float* ans, float* ansb, int n)
{
	float sum1 = 0;
	float sum2 = 0;

	for (int i = 0; i < n; i++)
	{
		sum1 = max(sum1, (ansb[i] - ans[i]));
		sum2 = max(sum1, ans[i]);
	}

	cout << setprecision(10) << "DEL: " << sum1 / sum2;

}

float** Create(int n, int m, float* a)
{
	float** Matrix = new float* [n];

	for (int i = 0; i < n; i++)
		Matrix[i] = new float[m];


	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < m; j++)
			Matrix[i][j] = a[i * m + j];
	}

	return Matrix;
}

float** CreateB(int n, int m, float* a, float* b)
{
	float** Matrix = new float* [n];

	for (int i = 0; i < n; i++)
		Matrix[i] = new float[m];

	int k = 0;

	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < m; j++)
		{
			if (j == m - 1)
			{
				Matrix[i][j] = b[k];
				k++;
			}
			else
				Matrix[i][j] = a[i * m + j];
		}
	}

	return Matrix;
}

void Out(float** a, int n, int m)
{
	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < m; j++)
			cout << setw(10) << a[i][j] << "\t\t";//horizontal tabulation
		cout << endl;
	}

	cout << endl << endl;
}

void SwapLines(int x, int x2, float** a)
{
	for (int j = 0; j < 4; j++)
		swap(a[x][j], a[x2][j]);
}

float* FindX2(float** Matrix, int n, int m)
{
	float maxi = Matrix[n - 1][m - 1];


	for (int i = 0; i < n; i++)
	{



		for (int l = i; l < n; l++)
		{
			if (Matrix[i][i] < Matrix[l][i])
				SwapLines(i, l, Matrix);
		}
		if (Matrix[i][i] == 0)
		{
			throw "DivByZero";
			exit(0);
		}

		maxi = Matrix[i][i];

		for (int j = i; j < m; j++)
		{
			Matrix[i][j] /= maxi;
		}

		for (int k = i + 1; k < n; k++)
		{
			float dp = Matrix[k][i];

			for (int j = i; j < m; j++)
				Matrix[k][j] -= Matrix[i][j] * dp;
		}
	}

	// Reverse

	float* ans = new float[n];
	ans[n - 1] = Matrix[n - 1][m - 1];

	for (int i = n - 2; i >= 0; i--)
	{
		float sum = 0;
		for (int k = i + 1; k < n; k++)
		{
			sum += Matrix[i][k] * ans[k];

		}
		ans[i] = Matrix[i][n] - sum;
	}

	return ans;
}

float* ResidualVect(float* ans, float* Matrix, int m, int n)
{
	float* newans = new float[n];

	for (int i = 0; i < n; i++)
		newans[i] = 0;

	int k = 0;
	int j = 0;
	float sum = 0;

	for (int i = 0; i < n * m; i++)
	{
		if (j == 3)
		{
			j = 0;
			k++;
			continue;
		}

		newans[k] += ans[j] * Matrix[i];
		j++;
	}

	j = 3;
	cout << "Residual Vector: " << setprecision(10) << endl;
	for (int i = 0; i < k; i++)
	{
		cout << newans[i] - Matrix[j] << " ";//arr[j] - newans[i] << " ";
		j += 4;
	}
	cout << endl;

	return newans;
}

void OutAns(float* ans, int n)
{
	cout << endl <<"Answers: " << endl;
	for (int i = 0; i < n; i++)
		cout << ans[i] << " ";
	cout << endl << endl;
}


GaussMeth.cpp



#include "Gauss.h"

int main()
{
	float** Matrix1;
	float** Matrix2;
	float* ans;
	float* b = new float(3);
	float* ansb;

	float a[12] = { 2.6, -4.5, -2, 19.07,
				  3, 3, 4.3, 3.21,
				  -6, 3.5, 3, -18.25 };

	Matrix1 = Create(3, 4, a);
	Out(Matrix1, 3, 4);

	try {
		ans = FindX2(Matrix1, 3, 4);
	}
	catch (const char* error_mesage)
	{
		cout << error_mesage;
		return 0;
	}


	Out(Matrix1, 3, 4);

	b = ResidualVect(ans, a, 4, 3);


	for (int i = 0; i < 3; i++)
		cout << " ! " << b[i] << endl;

	Matrix2 = CreateB(3, 4, a, b);


	Out(Matrix2, 3, 4);

	ansb = FindX2(Matrix2, 3, 4);


	OutAns(ans, 3);
	OutAns(ansb, 3);

	del(ans, ansb, 3);

	return 0;
}
