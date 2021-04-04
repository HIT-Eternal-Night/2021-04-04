# 2021-04-04
今天开始学习结构！新世界的大门打开了！

1、结构的运算：要访问整个结构，直接用结构变量的名字。
对于整个结构，可以做赋值、取地址，也可以传递给函数参数。
#include <stdio.h>

struct date {
	int month;
	int day;
	int year;
};

int main(int argc,char const *argv[])
{         
	struct date today;
	
	today = (struct date){04,04,2021};
	
	struct date day;
	
	day = today;
	day.year = 2022;  //day和today完全是两个东西，不是同一个
	
	printf("Today's date is %d-%d-%d.\n",
	today.year,today.month,today.day);
	printf("Today's date is %d-%d-%d.\n",
	day.year,day.month,day.day);  
    
	return 0;
}  

2、结构指针：
·和数组不同，结构变量的名字并不是它的地址，必须使用&运算符。
·struct date *pDate = &today;

3、结构作为函数参数
int numberOfDays(struct date d)
·整个结构可以作为参数的值传入函数，这时候是在函数内新建一个结构变量，
并复制调用者的结构的值。也可以返回一个结构，这与数组完全不同。

#include<stdio.h>
#include<stdbool.h>

struct date {
	int month;
	int day;
	int year;
};

bool isLeap(struct date d);
int numberOfDays(struct date d);
	
int main(int argc,char const *argv[])
{
	struct date today,tomorrow;

	printf("Enter today's date (mm dd yy):");
	scanf("%d %d %d",&today.month,&today.day,&today.year);
	
	if ( today.day != numberOfDays(today) ) 
	{
		tomorrow.day = today.day+1;
		tomorrow.month = today.month;
		tomorrow.year = today.year;
	} 
	else if ( today.month == 12)
	{
		tomorrow.day = 1;
		tomorrow.month = 1;
		tomorrow.year = today.year+1;
	} 
	else 
	{
		tomorrow.day = 1;
		tomorrow.month = today.month+1;
		tomorrow.year = today.year;
	}
	
	printf("Tomorrow's date is %d-%d-%d.\n",
	tomorrow.year,tomorrow.month,tomorrow.day);
	
	return 0;
}

int numberOfDays(struct date d)
{
	int days;
	
	const int daysPerMonth[12] = {31,28,31,30,31,30,31,31,30,31,30,31,};
	
	if (d.month == 2 && isLeap(d) )
		days = 29;
	else
		days = daysPerMonth[d.month-1];
	
	return days;//单入口单出口哦
}

bool isLeap(struct date d)
{
	bool leap = false;
	
	if ( (d.year % 4 == 0 && d.year % 100 != 0 ) || d.year % 400 == 0)
		leap = true;
	
	return leap;
}

4、输入结构：没有直接的方式可以一次scanf一个结构
·自己写一个函数直接读入结构在返回时遇到的问题同普通变量一样，也是只进行了传值，没法将结构变量保存结果带回。
·解决的方案：
之前的操作是把一个结构传入了函数，然后在函数中操作，但是无法返回。
问题在于传入函数的是外面那个结构的克隆体，而不是指针。
传入结构和传入数组是不同的。
现在，在这个输入函数中，完全可以创建一个临时的结构变量，然后把这个结构返回给调用者。

#include<stdio.h>

struct point {
	int x;
	int y;
};
	
struct point getStruct(void);
void output(struct point);

int main(int argc,char const *argv[])
{
	struct point y = {0, 0};
	y = getStruct();
	output(y);
	return 0;
}

struct point getStruct(void)
{
	struct point p;
	scanf("%d",&p.x);
	scanf("%d",&p.y);
	printf("%d, %d", p.x, p.y);
	return p;
}

void output(struct point p)
{
	printf("%d, %d", p.x, p.y);
}

5、指向结构的指针：
struct date {
	int year;
	int month;
	int day;
} myday;
struct date *p = &myday;
(*p).month = 12;
p->month = 12;

·用->表示指针所指的结构变量中的成员。

#include<stdio.h>

struct point {
	int x;
	int y;
};
	
struct point* getStruct(struct point *p);
void output(struct point p);
void print(const struct point *p);

int main(int argc,char const *argv[])
{
	struct point m = {0, 0};
	getStruct(&m);
	output(m);    //三种方式都可以用于输出
	output(*getStruct(&m));
	print(getStruct(&m));
	return 0;
}

struct point* getStruct(struct point *p)
{
	scanf("%d",&p->x);
	scanf("%d",&p->y);
	printf("%d, %d", p->x, p->y);
	return p;
}

void output(struct point p)
{
	printf("%d, %d", p.x, p.y);
}

void print(const struct point *p)
{
	printf("%d, %d",p->x, p->y);
}
