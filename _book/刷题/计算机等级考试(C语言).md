2019.10.20

当时最后一道编程题没做出来，题目如下

题目：输入一个英文句子，翻转句子中单词的顺序，但单词内字符的顺序不变。句子中单词以空格符隔开。标点符号和普通字母一样处理。(还要写到文件中)

例如输入“student. a am I”，则输出“I am a student.”。

思路：先将字符串整体翻转一遍，此时单词之间的顺序是翻转了，可是单词内部的顺序也翻转了，所以在一个个单词处理，将单词重新翻转过来。

1.student. a am I --> I ma a .tneduts

2.I ma a .tneduts --> I am a student.

```C
#include<stdio.h>
#include<string.h>
void reverse(char *s, int begin, int end)
{
	char temp;
	while(end > begin) {
		temp = s[begin];
		s[begin] = s[end];
		s[end] = temp;
		begin++;
		end--;
	}
	
}
int main()
{
	int a, b;
	a = b = 0;
	char s[100];
	gets(s);
	reverse(s, 0, strlen(s)-1);
	int i = 0;
	while(i < strlen(s)) {
		while(s[i] == ' ' && i < strlen(s)) {
			i++;
		}
		a = i;
		while(s[i] != ' ' && i < strlen(s)) {
			i++;
		}
		b = i-1;
		reverse(s,a, b);
	}
	puts(s);
	return 0;
}
```

