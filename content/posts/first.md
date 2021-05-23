---
title: "First"
date: 2021-04-15T23:12:48-03:00
draft: false
---

Average example implementation on C++:
``` cpp
#include <omp.h>
#include <iostream>
#include <vector>
#include <numeric>
using namespace std;
 
// Non-parallel average function
double avg(const vector<double> &v)
{
	double sum = accumulate(v.begin(), v.end(), 0.0);
	return sum/v.size();
}
 
// Parallel average function
double pavg(const vector<double> &v)
{
	// to do
}
 
int main()
{
	vector<double> v(1000000);
	for (int i = 0 ; i < v.size() ; i ++)
		v[i] = 2*i;
 
	cout << "Average: " << avg(v) << endl;
	cout << "Parallel average: " << pavg(v) << endl;
 
	return 0;
}
```