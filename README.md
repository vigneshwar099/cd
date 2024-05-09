exp 1 
#include <ctype.h>
#include <stdbool.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define MAX_LENGTH 100
bool isDelimiter(char chr)
{
return (chr == ' ' || chr == '+' || chr == '-' ||
chr == '*' || chr == '/' || chr == ',' ||
chr == ';' || chr == '%' || chr == '>' ||
chr == '<' || chr == '=' || chr == '(' ||
chr == ')' || chr == '[' || chr == ']' ||
chr == '{' || chr == '}');
}
bool isOperator(char chr)
{
return (chr == '+' || chr == '-' || chr == '*' ||
chr == '/' || chr == '>' || chr == '<' ||
chr == '=');
}
bool isValidIdentifier(char* str)
{
return (str[0] != '0' && str[0] != '1' && str[0] != '2' &&
str[0] != '3' && str[0] != '4' &&
str[0] != '5' && str[0] != '6' &&
str[0] != '7' && str[0] != '8' &&
str[0] != '9' && !isDelimiter(str[0]));
}
bool isKeyword(char* str)
{
const char* keywords[] = { "auto", "break", "case", "char", "const", "continue", "default", "do",
"double", "else", "enum", "extern", "float", "for", "goto", "if", "int", "long", "register", "return",
"short", "signed", "sizeof", "static", "struct", "switch", "typedef", "union", "unsigned", "void",
"volatile", "while" };
for (int i = 0; i < sizeof(keywords) / sizeof(keywords[0]); i++) {
if (strcmp(str, keywords[i]) == 0) {
return true;
}
}
return false;
}
bool isInteger(char* str)
{
if (str == NULL || *str == '\0') {
return false;
}
int i = 0;
while (isdigit(str[i])) {
i++;
}
return str[i] == '\0';
}
char* getSubstring(char* str, int start, int end)
{
int length = strlen(str);
int subLength = end - start + 1;
char* subStr = (char*)malloc((subLength + 1) * sizeof(char));
strncpy(subStr, str + start, subLength);
subStr[subLength] = '\0';
return subStr;
}
int lexicalAnalyzer(char* input)
{
int left = 0, right = 0;
int len = strlen(input);
while (right <= len && left <= right) {
if (!isDelimiter(input[right]))
right++;
if (isDelimiter(input[right]) && left == right) {
if (isOperator(input[right]))
printf("Token: Operator, Value: %c\n", input[right]);
right++;
left = right;
}
else if (isDelimiter(input[right]) && left != right
|| (right == len && left != right)) {
char* subStr = getSubstring(input, left, right - 1);
if (isKeyword(subStr))
printf("Token: Keyword, Value: %s\n", subStr);
else if (isInteger(subStr))
printf("Token: Integer, Value: %s\n", subStr);
else if (isValidIdentifier(subStr) && !isDelimiter(input[right - 1]))
printf("Token: Identifier, Value: %s\n", subStr);
else if (!isValidIdentifier(subStr) && !isDelimiter(input[right - 1]))
printf("Token: Unidentified, Value: %s\n", subStr);
left = right;
}
}
return 0;
}
int main()
{
char lex_input[MAX_LENGTH] = "int a = b + c";
printf("For Expression \"%s\":\n", lex_input);
lexicalAnalyzer(lex_input);
printf(" \n");
char lex_input01[MAX_LENGTH] = "int x=ab+bc+30+switch+ 0y ";
printf("For Expression \"%s\":\n", lex_input01);
lexicalAnalyzer(lex_input01);
return (0);
}

exp 2
#include<stdio.h>
#include<string.h>
int main() {
char reg[20];
int q[20][3], i = 0, j = 1, len, a, b;
for (a = 0; a < 20; a++)
for (b = 0; b < 3; b++)
q[a][b] = 0;
scanf("%s", reg);
printf("Given regular expression: %s\n", reg);
len = strlen(reg);
while (i < len) {
if (reg[i] == 'a' && reg[i + 1] != '|' && reg[i + 1] != '*') {
q[j][0] = j + 1;
j++;
}
if (reg[i] == 'b' && reg[i + 1] != '|' && reg[i + 1] != '*') {
q[j][1] = j + 1;
j++;
}
if (reg[i] == 'e' && reg[i + 1] != '|' && reg[i + 1] != '*') {
q[j][2] = j + 1;
j++;
}
if (reg[i] == 'a' && reg[i + 1] == '|' && reg[i + 2] == 'b') {
q[j][2] = ((j + 1) * 10) + (j + 3);
j++;
q[j][0] = j + 1;
j++;
q[j][2] = j + 3;
j++;
q[j][1] = j + 1;
j++;
q[j][2] = j + 1;
j++;
i = i + 2;
}
if (reg[i] == 'b' && reg[i + 1] == '|' && reg[i + 2] == 'a') {
q[j][2] = ((j + 1) * 10) + (j + 3);
j++;
q[j][1] = j + 1;
j++;
q[j][2] = j + 3;
j++;
q[j][0] = j + 1;
j++;
q[j][2] = j + 1;
j++;
i = i + 2;
}
if (reg[i] == 'a' && reg[i + 1] == '*') {
q[j][2] = ((j + 1) * 10) + (j + 3);
j++;
q[j][0] = j + 1;
j++;
q[j][2] = ((j + 1) * 10) + (j - 1);
j++;
}
if (reg[i] == 'b' && reg[i + 1] == '*') {
q[j][2] = ((j + 1) * 10) + (j + 3);
j++;
q[j][1] = j + 1;
j++;
q[j][2] = ((j + 1) * 10) + (j - 1);
j++;
}
if (reg[i] == ')' && reg[i + 1] == '*') {
q[0][2] = ((j + 1) * 10) + 1;
q[j][2] = ((j + 1) * 10) + 1;
j++;
}
i++;
}
printf("\n\tTransition Table\n");
printf("_____________\n");
printf("Current State |\tInput |\tNext State");
printf("\n_____________\n");
for (i = 0; i <= j; i++) {
if (q[i][0] != 0) printf("\n q[%d]\t | a | q[%d]", i, q[i][0]);
if (q[i][1] != 0) printf("\n q[%d]\t | b | q[%d]", i, q[i][1]);
if (q[i][2] != 0) {
if (q[i][2] < 10) printf("\n q[%d]\t | e | q[%d]", i, q[i][2]);
else printf("\n q[%d]\t | e | q[%d] , q[%d]", i, q[i][2] / 10, q[i][2] % 10);
}
}
printf("\n_____________\n");
return 0;
}

exp 3
#include<stdio.h>
#include<string.h>
int n[11], I, j, c, k, h, l, h1, f, h2, temp1[12], temp2[12], count = 0, ptr = 0;
char a[20][20], s[5][8], st[5], tr[5][2], ecl[5][8];
int flag;
void ecls(int b[10], int x) {
int i = 0;
int k = -1;
flag = 0;
while (i < x) {
n[++k] = b[i];
i = b[i];
h = k + 1;
a:
for (int j = i; j <= 11; j++) {
if (a[i][j] == 'e') {
n[++k] = j;
}
if (j == 11 && h <= k) {
i = n[h];
h++;
goto a;
}
}
i++;
}
l++;
}
void mova(int g) {
h1 = 0;
for (int i = 0; i < 7; i++) {
if (ecl[g][i] == 3) {
temp1[h1++] = 4;
}
if (ecl[g][i] == 8) {
temp1[h1++] = 9;
}
}
printf("\nmove(%c,a):", st[g]);
for (int i = 0; i < h1; i++) {
printf("%d", temp1[i]);
}
f = 0;
ecls(temp1, h1);
}
void movb(int g) {
h2 = 0;
for (int i = 0; i < 7; i++) {
if (ecl[g][i] == 5) {
temp2[h2++] = 6;
}
if (ecl[g][i] == 9) {
temp2[h2++] = 10;
}
if (ecl[g][i] == 10) {
temp2[h2++] = 11;
}
}
printf("\nmove(%c,b):", st[g]);
for (int i = 0; i < h2; i++) {
printf("%d", temp2[i]);
}
f = 1;
ecls(temp2, h2);
}
int main() {
printf("\nthe no. of states in NFA (a/b)*abb are: 11");
for (int i = 0; i <= 11; i++) {
for (int j = 0; j <= 11; j++) {
a[i][j] = '\0';
}
}
a[1][2] = 'e';
a[1][8] = 'e';
a[2][3] = 'e';
a[2][5] = 'e';
a[3][4] = 'a';
a[5][6] = 'b';
a[4][7] = 'e';
a[6][7] = 'e';
a[7][8] = 'e';
a[7][2] = 'e';
a[8][9] = 'a';
a[9][10] = 'b';
a[10][11] = 'b';
printf("\n\nThe transition table is as follows:\n");
printf(" ");
for (int i = 1; i <= 11; i++) {
printf("%d ", i);
}
printf("\n");
for (int i = 1; i <= 11; i++) {
printf("%d ", i);
for (int j = 1; j <= 11; j++) {
printf("%c ", a[i][j]);
}
printf("\n");
}
return 0;
}

