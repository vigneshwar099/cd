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

exp 4 a
 #include<stdio.h>
#include<string.h>
void main() {
char input[100],l[50],r[50],temp[10],tempprod[20],productions[25][50];
int i=0,j=0,flag=0,consumed=0;
printf("Enter the productions: ");
scanf("%1s->%s",l,r);
printf("%s",r);
while(sscanf(r+consumed,"%[^|]s",temp) == 1 && consumed <= strlen(r)) {
if(temp[0] == l[0]) {
flag = 1;
sprintf(productions[i++],"%s->%s%s'\0",l,temp+1,l);
} else
sprintf(productions[i++],"%s'->%s%s'\0",l,temp,l);
consumed += strlen(temp)+1;
}
if(flag == 1) {
sprintf(productions[i++],"%s->ε\0",l);
printf(" The productions after eliminating Left Recursion are:\n");
for(j=0;j<i;j++)
printf("%s\n",productions[j]);
} else
printf("The Given Grammar has no Left Recursion");
}
4b
 #include <stdio.h>
#include <string.h>
int main() {
char gram[20], part1[20], part2[20], modifiedGram[20], newGram[20];
int i, j = 0, k = 0, pos;
printf("Enter Production: A -> ");
scanf("%s", gram);
for (i = 0; gram[i] != '|'; i++, j++)
part1[j] = gram[i];
part1[j] = '\0';
for (j = ++i, i = 0; gram[j] != '\0'; j++, i++)
part2[i] = gram[j];
part2[i] = '\0';
for (i = 0; i < strlen(part1) || i < strlen(part2); i++) {
if (part1[i] == part2[i]) {
modifiedGram[k] = part1[i];
k++;
pos = i + 1; } }
for (i = pos, j = 0; part1[i] != '\0'; i++, j++)
newGram[j] = part1[i];
newGram[j++] = '|';
for (i = pos; part2[i] != '\0'; i++, j++)
newGram[j] = part2[i];
modifiedGram[k] = 'X';
modifiedGram[++k] = '\0';
newGram[j] = '\0';
printf("\nGrammar Without Left Factoring:\n");
printf("A -> %s\n", modifiedGram);
printf("X -> %s\n", newGram);
return 0;
}
 exp 4 c
#include <stdio.h>
#include <conio.h>
void main() {
int nop, x=0, y=0, l=0, k=0, c=0, s=0, z=0;
char p[10][10], fi[10][10], fo[10][10];
for(x=0; x<10; x++) {
for(y=0; y<10; y++) {
p[x][y]='0';
fi[x][y]='0';
fo[x][y]='0';
}
}
printf("Enter the no of productions: ");
scanf("%d", &nop);
printf("\nEnter the productions:\n");
for(x=0; x<nop; x++) {
scanf("%s", p[x]);
}
printf("\nFirst\n");
for(y=0; y<nop; y++) {
printf("First(%c)={", p[y][0]);
if(p[y][2] == 'e' || (p[y][2] >= 'a' && p[y][2] <= 'z') || (p[y][2] >= 'A' && p[y][2] <= 'Z')) {
printf("%c", p[y][2]);
fi[y][k++]=p[y][2];
}
else if(p[y][2] == '(' || (p[y][2] >= 'a' && p[y][2] <= 'z') || (p[y][2] >= 'A' && p[y][2] <= 'Z')) {
printf("%c", p[y][2]);
fi[y][k++]=p[y][2];
}
printf("}\n");
}
printf("\nFollow\n");
for(x=0; x<nop; x++) {
printf("Follow(%c)={", p[x][0]);
if(x == 0) {
printf("$");
}
printf("}\n");
}
getch();
}
 🐒: exp 5
🐒: #include <stdio.h>
#include <string.h>
char str[10], out, in, output[10], input[10], temp;
char tl[7] = {'x', '+', '*', '(', ')', '$', '@'};
char ntl[5] = {'e', 'e', 't', 't', 'f'};
int err = 0, flag = 0, i, j, k, m;
char c[5][6][7] = {
{"te", "error!", "error!", "te", "error!", "error!"},
{"error!", "te", "error!", "error", "@", "@"},
{"ft", "error!", "error!", "ft", "error!", "error!"},
{"error!", "@", "*ft", "error!", "@", "@"},
{"x", "error!", "error!", "(e)", "error!", "error!"}};
struct stack
{
char sic[10];
int top;
};
void push(struct stack *s, char p)
{
s->sic[++s->top] = p;
s->sic[s->top + 1] = '\0';
}
char pop(struct stack *s)
{
char a;
a = s->sic[s->top];
s->sic[s->top--] = '\0';
return (a);
}
char stop(struct stack *s)
{
return (s->sic[s->top]);
}
void pobo(struct stack *s)
{
m = 0;
while (str[m] != '\0')
m++;
m--;
while (m != -1)
{
if (str[m] != '@')
push(s, str[m]);
m--;
}
}
void search(int l)
{
for (k = 0; k < 7; k++)
if (in == tl[k])
break;
if (l == 0)
strcpy(str, c[l][k]);
else if (l == 1)
strcpy(str, c[l][k]);
else if (l == 2)
strcpy(str, c[l][k]);
else if (l == 3)
strcpy(str, c[l][k]);
else
strcpy(str, c[l][k]);
}
int main()
{
struct stack s1;
struct stack *s;
s = &s1;
s->top = -1;
printf("\t\t Parsing Table \t\t");
for (i = 0; i < 5; i++)
{
printf("%c\t", ntl[i]);
for (j = 0; j < 6; j++)
if (strcmp(c[i][j], "error!") == 0)
printf("error!\t");
else
printf("%c->%s\t", ntl[i], c[i][j]);
printf("\n");
}
push(s, '$');
push(s, 'e');
printf("Enter the input string: ");
scanf("%s", input);
printf("\n\nThe behavior of the parser for the given input string is: \n");
printf("\nStack\tInput\tOutput");
i = 0;
in = input[i];
printf("%s\t", s->sic);
for (int k = i; k < strlen(input); k++)
printf("%c", input[k]);
if (strcmp(str, " ") != 0)
printf("\t%c->%s", ntl[j], str);
while ((s->sic[s->top] != '$') && err != 1 && strcmp(str, "error!") != 0)
{
strcpy(str, " ");
flag = 0;
for (j = 0; j < 7; j++)
if (in == tl[j])
{
flag = 1;
break;
}
if (flag == 0)
in = 'x';
flag = 0;
out = stop(s);
for (j = 0; j < 7; j++)
if (out == tl[j])
{
flag = 1;
break;
}
if (flag == 1)
{
if (out == in)
{
temp = pop(s);
in = input[++i];
if (strcmp(str, "@") == 0)
temp = pop(s);
}
else
{
strcpy(str, "error!");
err = 1;
}
}
else
{
flag = 0;
for (j = 0; j < 5; j++)
if (out == ntl[j])
{
flag = 1;
break;
}
if (flag == 1)
{
search(j);
temp = pop(s);
pobo(s);
}
else
{
strcpy(str, "error!");
err = 1;
}
}
if (strcmp(str, "error!") != 0)
{
printf("%s\t", s->sic);
for (int k = i; k < strlen(input); k++)
printf("%c", input[k]);
if ((strcmp(str, " ") != 0) && (strcmp(str, "error!") != 0))
printf("\t%c->%s", ntl[j], str);
}
}
if (strcmp(str, "error!") == 0)
printf("\nThe string is not accepted!!");
else
printf("\t\tAccepted.\n\n\nThe string is accepted.");
return 0;
}
🐒: exp 6
🐒: #include<stdio.h>
#include<stdlib.h>
#include<string.h>
int z = 0, i = 0, j = 0, c = 0;
char a[16], ac[20], stk[15], act[10];
void check()
{
strcpy(ac,"REDUCE TO E -> ");
for(z = 0; z < c; z++)
{
if(stk[z] == '4')
{
printf("%s4", ac);
stk[z] = 'E';
stk[z + 1] = '\0';
printf("\n$%s\t%s$\t", stk, a);
}
}
for(z = 0; z < c - 2; z++)
{
if(stk[z] == '2' && stk[z + 1] == 'E' && stk[z + 2] == '2')
{
printf("%s2E2", ac);
stk[z] = 'E';
stk[z + 1] = '\0';
stk[z + 2] = '\0';
printf("\n$%s\t%s$\t", stk, a);
i = i - 2;
}
}
for(z=0; z<c-2; z++)
{
if(stk[z] == '3' && stk[z + 1] == 'E' &&
stk[z + 2] == '3')
{
printf("%s3E3", ac);
stk[z]='E';
stk[z + 1]='\0';
stk[z + 1]='\0';
printf("\n$%s\t%s$\t", stk, a);
i = i - 2;
}
}
return ;
}
int main()
{
printf("GRAMMAR is -\nE->2E2 \nE->3E3 \nE->4\n");
strcpy(a,"32423");
c=strlen(a);
strcpy(act,"SHIFT");
printf("\nstack \t input \t action");
printf("\n$\t%s$\t", a);
for(i = 0; j < c; i++, j++)
{
printf("%s", act);
stk[i] = a[j];
stk[i + 1] = '\0';
a[j]=' ';
printf("\n$%s\t%s$\t", stk, a);
check();
}
check();
if(stk[0] == 'E' && stk[1] == '\0')
printf("Accept\n");
else //else reject
printf("Reject\n");
}
🐒: exp 7
 🐒: #include<stdio.h>
#include<string.h>
#include<conio.h>
int nt, t, top = 0;
char s[50], NT[10], T[10], st[50], l[10][10], tr[50][50];
int searchnt(char a) {
int count = -1, i;
for (i = 0; i < nt; i++) {
if (NT[i] == a)
return i;
}
return count;
}
int searchter(char a) {
int count = -1, i;
for (i = 0; i < t; i++) {
if (T[i] == a)
return i;
}
return count;
}
void push(char a) {
s[top] = a;
top++;
}
char pop() {
top--;
return s[top];
}
void installl(int a, int b) {
if (l[a][b] == 'f') {
l[a][b] = 't';
push(T[b]);
push(NT[a]);
}
}
void installt(int a, int b) {
if (tr[a][b] == 'f') {
tr[a][b] = 't';
push(T[b]);
push(NT[a]);
}
}
int main() {
int i, s, k, j, n;
char pr[30][30], b, c;
clrscr();
printf("Enter the no of productions:");
scanf("%d", &n);
printf("Enter the productions one by one\n");
for (i = 0; i < n; i++)
scanf("%s", pr[i]);
nt = 0;
t = 0;
for (i = 0; i < n; i++) {
if ((searchnt(pr[i][0])) == -1)
NT[nt++] = pr[i][0];
}
for (i = 0; i < n; i++) {
for (j = 3; j < strlen(pr[i]); j++) {
if (searchnt(pr[i][j]) == -1) {
if (searchter(pr[i][j]) == -1)
T[t++] = pr[i][j];
}
}
}
for (i = 0; i < nt; i++) {
for (j = 0; j < t; j++)
l[i][j] = 'f';
}
for (i = 0; i < nt; i++) {
for (j = 0; j < n; j++) {
if (NT[(searchnt(pr[j][0]))] == NT[i]) {
if (searchter(pr[j][3]) != -1)
installl(searchnt(pr[j][0]), searchter(pr[j][3]));
else {
for (k = 3; k < strlen(pr[j]); k++) {
if (searchnt(pr[j][k]) == -1) {
installl(searchnt(pr[j][0]), searchter(pr[j][k]));
break;
}
}
}
}
}
}
while (top != 0) {
b = pop();
c = pop();
for (s = 0; s < n; s++) {
if (pr[s][3] == b)
installl(searchnt(pr[s][0]), searchter(c));
}
}
for (i = 0; i < nt; i++) {
printf("Leading[%c]\t{", NT[i]);
for (j = 0; j < t; j++) {
if (l[i][j] == 't')
printf("%c ", T[j]);
}
printf("}\n");
}
top = 0;
for (i = 0; i < nt; i++) {
for (j = 0; j < n; j++) {
if (NT[searchnt(pr[j][0])] == NT[i]) {
if (searchter(pr[j][strlen(pr[j]) - 1]) != -1)
installt(searchnt(pr[j][0]), searchter(pr[j][strlen(pr[j]) - 1]));
else {
for (k = (strlen(pr[j]) - 1); k >= 3; k--) {
if (searchnt(pr[j][k]) == -1) {
installt(searchnt(pr[j][0]), searchter(pr[j][k]));
break;
}
}
}
}
}
}
while (top != 0) {
b = pop();
c = pop();
for (s = 0; s < n; s++) {
if (pr[s][3] == b)
installt(searchnt(pr[s][0]), searchter(c));
}
}
for (i = 0; i < nt; i++) {
printf("Trailing[%c]\t{", NT[i]);
for (j = 0; j < t; j++) {
if (tr[i][j] == 't')
printf("%c ", T[j]);
}
printf("}\n");
}
getch();
return 0;
}
 🐒: exp 8
 🐒: #include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define NUM_STATES 8
#define NUM_SYMBOLS 5
enum Symbols {
SYMBOL_E = 0,
SYMBOL_T,
SYMBOL_F,
SYMBOL_PLUS,
SYMBOL_MULTIPLY
};
int parsingTable[NUM_STATES][NUM_SYMBOLS] = {
{1, 2, 3, -1, -1},
{-1, -1, -1, 4, -1},
{-1, -1, -1, -1, 5},
{6, 2, 3, -1, -1},
{-1, -1, -1, -1, 7},
{-1, -1, -1, -1, -1},
{1, 2, 3, -1, -1},
{-1, -1, -1, -1, -1}
};
void shift(int *stateStack, int *symbolStack, int *top, int nextState, int symbol);
void reduce(int *stateStack, int *symbolStack, int *top, int rule);
void parseInput();
int main() {
parseInput();
return 0;
}
void shift(int *stateStack, int *symbolStack, int *top, int nextState, int symbol) {
stateStack[++(*top)] = nextState;
symbolStack[*top] = symbol;
}
void reduce(int *stateStack, int *symbolStack, int *top, int rule) {
int i;
switch (rule) {
case 1:
for (i = 0; i < 3; i++) {
(*top)--;
}
stateStack[*top + 1] = parsingTable[stateStack[*top]][SYMBOL_E];
symbolStack[*top + 1] = SYMBOL_E;
(*top)++;
break;
case 2:
for (i = 0; i < 3; i++) {
(*top)--;
}
stateStack[*top + 1] = parsingTable[stateStack[*top]][SYMBOL_T];
symbolStack[*top + 1] = SYMBOL_T;
(*top)++;
break;
case 3:
for (i = 0; i < 3; i++) {
(*top)--;
}
stateStack[*top + 1] = parsingTable[stateStack[*top]][SYMBOL_F];
symbolStack[*top + 1] = SYMBOL_F;
(*top)++;
break;
}
}
void parseInput() {
char input[] = "a+b*c";
int stateStack[100], symbolStack[100], top = 0;
stateStack[0] = 0;
printf("Enter input string: %s\n", input);
printf("Stack\tInput\tAction\n");
printf("-----\t-----\t------\n");
int i = 0;
while (input[i] != '\0') {
for (int j = 0; j <= top; j++) {
printf("%d%c", stateStack[j], symbolStack[j] != -1 ? symbolStack[j] + 'A' : ' ');
}
printf("\t%s\t", input + i);
char currentSymbol = input[i];
int symbol;
switch (currentSymbol) {
case 'a':
symbol = SYMBOL_E;
break;
case 'b':
symbol = SYMBOL_T;
break;
case 'c':
symbol = SYMBOL_F;
break;
case '+':
symbol = SYMBOL_PLUS;
break;
case '*':
symbol = SYMBOL_MULTIPLY;
break;
default:
printf("\nInvalid symbol\n");
return;
}
int action = parsingTable[stateStack[top]][symbol];
if (action == -1) {
printf("\nError\n");
return;
} else if (action > 0 && action < NUM_STATES) {
shift(stateStack, symbolStack, &top, action, symbol);
i++;
printf("Shift\n");
} else {
reduce(stateStack, symbolStack, &top, abs(action));
printf("Reduce by rule %d\n", abs(action));
}
}
printf("Accepted\n");
}
 🐒: exp 9
 🐒: #include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#define MAX_SIZE 100
// Stack structure
typedef struct {
int top;
char items[MAX_SIZE];
} Stack;
// Function prototypes
void push(Stack *s, char value);
char pop(Stack *s);
int isOperator(char c);
int precedence(char c);
void infixToPostfix(char *infix, char *postfix);
void infixToPrefix(char *infix, char *prefix);
int main() {
char infixExpression[MAX_SIZE];
char postfixExpression[MAX_SIZE];
char prefixExpression[MAX_SIZE];
// Input infix expression
printf("Enter infix expression: ");
scanf("%s", infixExpression);
infixToPostfix(infixExpression, postfixExpression);
infixToPrefix(infixExpression, prefixExpression);
printf("Postfix expression: %s\n", postfixExpression);
printf("Prefix expression: %s\n", prefixExpression);
return 0;
}
// Function to push an item onto the stack
void push(Stack *s, char value) {
if (s->top == MAX_SIZE - 1) {
printf("Stack overflow\n");
exit(EXIT_FAILURE);
}
s->items[++s->top] = value;
}
// Function to pop an item from the stack
char pop(Stack *s) {
if (s->top == -1) {
printf("Stack underflow\n");
exit(EXIT_FAILURE);
}
return s->items[s->top--];
}
// Function to check if a character is an operator
int isOperator(char c) {
return (c == '+' || c == '-' || c == '*' || c == '/');
}
// Function to return precedence of operators
int precedence(char c) {
if (c == '*' || c == '/')
return 2;
else if (c == '+' || c == '-')
return 1;
else
return 0;
}
// Function to convert infix expression to postfix expression
void infixToPostfix(char *infix, char *postfix) {
Stack s;
s.top = -1;
int i = 0, j = 0;
while (infix[i] != '\0') {
if (isalnum(infix[i])) {
postfix[j++] = infix[i];
} else if (infix[i] == '(') {
push(&s, infix[i]);
} else if (infix[i] == ')') {
while (s.top != -1 && s.items[s.top] != '(') {
postfix[j++] = pop(&s);
}
if (s.top == -1) {
printf("Invalid infix expression\n");
exit(EXIT_FAILURE);
}
pop(&s); // Discard the '('
} else if (isOperator(infix[i])) {
while (s.top != -1 && precedence(s.items[s.top]) >= precedence(infix[i])) {
postfix[j++] = pop(&s);
}
push(&s, infix[i]);
}
i++;
}
while (s.top != -1) {
if (s.items[s.top] == '(') {
printf("Invalid infix expression\n");
exit(EXIT_FAILURE);
}
postfix[j++] = pop(&s);
}
postfix[j] = '\0';
}
// Function to convert infix expression to prefix expression
void infixToPrefix(char *infix, char *prefix) {
Stack s;
s.top = -1;
char temp[MAX_SIZE];
int i, j = 0;
// Reverse the infix expression
for (i = strlen(infix) - 1; i >= 0; i--) {
if (infix[i] == '(')
temp[j++] = ')';
else if (infix[i] == ')')
temp[j++] = '(';
else
temp[j++] = infix[i];
}
temp[j] = '\0';
infixToPostfix(temp, prefix);
// Reverse the postfix expression to get the prefix expression
j = strlen(prefix) - 1;
for (i = 0; i < j; i++, j--) {
char tempChar = prefix[i];
prefix[i] = prefix[j];
prefix[j] = tempChar;
}
}
 🐒: exp 10
 🐒: #include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define MAX_SIZE 100
// Structure for Quadruple
typedef struct {
char op;
char arg1[MAX_SIZE];
char arg2[MAX_SIZE];
char result[MAX_SIZE];
} Quadruple;
// Structure for Triple
typedef struct {
char op[3]; // To store operator as a string
char arg1[MAX_SIZE];
char arg2[MAX_SIZE];
} Triple;
// Structure for Indirect Triple
typedef struct {
char op[3]; // To store operator as a string
int arg1;
int arg2;
} IndirectTriple;
// Function prototypes
void generateQuadruples(char *expression);
void generateTriples(char *expression);
void generateIndirectTriples(char *expression);
int main() {
char expression[MAX_SIZE];
// Input expression
printf("Enter the expression: ");
fgets(expression, MAX_SIZE, stdin);
generateQuadruples(expression);
generateTriples(expression);
generateIndirectTriples(expression);
return 0;
}
// Function to generate quadruples
void generateQuadruples(char *expression) {
Quadruple quadruples[MAX_SIZE];
int index = 0;
// Tokenize the expression
char token = strtok(expression, "+-/");
while (token != NULL) {
Quadruple quad;
quad.op = expression[strlen(token)];
strcpy(quad.arg1, token);
strcpy(quad.arg2, token + 2);
sprintf(quad.result, "T%d", index + 1);
quadruples[index++] = quad;
token = strtok(NULL, "+-*/");
}
// Print the quadruples
printf("Quadruples:\n");
for (int i = 0; i < index; i++) {
printf("(%c, %s, %s, %s)\n", quadruples[i].op, quadruples[i].arg1, quadruples[i].arg2,
quadruples[i].result);
}
}
// Function to generate triples
void generateTriples(char *expression) {
Triple triples[MAX_SIZE];
int index = 0;
// Tokenize the expression
char token = strtok(expression, "+-/");
while (token != NULL) {
Triple triple;
strcpy(triple.op, &expression[strlen(token)]);
strcpy(triple.arg1, token);
strcpy(triple.arg2, token + 2);
triples[index++] = triple;
token = strtok(NULL, "+-*/");
}
// Print the triples
printf("\nTriples:\n");
for (int i = 0; i < index; i++) {
printf("(%s, %s, %s)\n", triples[i].op, triples[i].arg1, triples[i].arg2);
}
}
// Function to generate indirect triples
void generateIndirectTriples(char *expression) {
IndirectTriple indirectTriples[MAX_SIZE];
int index = 0;
// Tokenize the expression
char token = strtok(expression, "+-/");
while (token != NULL) {
IndirectTriple indirectTriple;
strcpy(indirectTriple.op, &expression[strlen(token)]);
indirectTriple.arg1 = index;
indirectTriple.arg2 = index + 1;
indirectTriples[index++] = indirectTriple;
token = strtok(NULL, "+-*/");
}
// Print the indirect triples
printf("\nIndirect Triples:\n");
for (int i = 0; i < index; i++) {
printf("(%s, %d, %d)\n", indirectTriples[i].op, indirectTriples[i].arg1, indirectTriples[i].arg2);
}
}
🐒: exp 11
🐒: #include <stdio.h>
#include <string.h>
void intermediateCodeGen();
int main() {
intermediateCodeGen();
return 0;
}
void intermediateCodeGen() {
char op[2], arg1[5], arg2[5], result[5];
int i = 0;
char input[] = {"+ a b t1", " c d t2", "- t1 t2 t", "= t ? x", NULL};
printf("Intermediate Code Generation:\n");
while (input[i] != NULL) {
sscanf(input[i], "%s%s%s%s", op, arg1, arg2, result);
if (strcmp(op, "+") == 0) {
printf("MOV R0,%s\n", arg1);
printf("ADD R0,%s\n", arg2);
printf("MOV %s,R0\n", result);
} else if (strcmp(op, "*") == 0) {
printf("MOV R0,%s\n", arg1);
printf("MUL R0,%s\n", arg2);
printf("MOV %s,R0\n", result);
} else if (strcmp(op, "-") == 0) {
printf("MOV R0,%s\n", arg1);
printf("SUB R0,%s\n", arg2);
printf("MOV %s,R0\n", result);
} else if (strcmp(op, "/") == 0) {
printf("MOV R0,%s\n", arg1);
printf("DIV R0,%s\n", arg2);
printf("MOV %s,R0\n", result);
} else if (strcmp(op, "=") == 0) {
printf("MOV R0,%s\n", arg1);
printf("MOV %s,R0\n", result);
}
i++;
}
}
 🐒: exp 12
 🐒: #include<stdio.h>
#include<stdlib.h>
#include<time.h>
#define EDGES 20
typedef struct {
int source;
int dest;
} Edge;
int compareEdges(const void* a, const void* b) {
Edge* edge1 = (Edge*)a;
Edge* edge2 = (Edge*)b;
return (edge1->source - edge2->source);
}
int main() {
int i;
Edge edges[EDGES];
srand(time(NULL));
printf("DIRECTED ACYCLIC GRAPH\n");
for (i = 0; i < EDGES; i++) {
edges[i].source = rand() % 10;
edges[i].dest = rand() % 10;
}
qsort(edges, EDGES, sizeof(Edge), compareEdges);
for (i = 0; i < EDGES; i++) {
printf("%d -> %d;\n", edges[i].source, edges[i].dest);
}
return 0;
}

 🐒: exp 13
 🐒: #include<stdio.h>
#include<string.h>
#include<ctype.h>
void input();
void output();
void change(int p, int q, char *res);
void constant();
void expression();
struct expr {
char op[2], op1[5], op2[5], res[5];
int flag;
} arr[10];
int n;
int main() {
input();
constant();
expression();
output();
return 0;
}
void input() {
printf("\nEnter the number of expressions: ");
scanf("%d", &n);
printf("\nEnter the input expressions: \n");
for (int i = 0; i < n; i++) {
scanf("%s %s %s %s", arr[i].op, arr[i].op1, arr[i].op2, arr[i].res);
arr[i].flag = 0;
}
}
void constant() {
int op1, op2, res;
char op, res1[5];
for (int i = 0; i < n; i++) {
if (isdigit(arr[i].op1[0]) && isdigit(arr[i].op2[0])) {
op1 = atoi(arr[i].op1);
op2 = atoi(arr[i].op2);
op = arr[i].op[0];
switch(op) {
case '+':
res = op1 + op2;
break;
case '-':
res = op1 - op2;
break;
case '*':
res = op1 * op2;
break;
case '/':
res = op1 / op2;
break;
}
sprintf(res1, "%d", res);
arr[i].flag = 1;
change(i, i, res1);
}
}
}
void expression() {
for (int i = 0; i < n; i++) {
for (int j = i + 1; j < n; j++) {
if (strcmp(arr[i].op, arr[j].op) == 0) {
if (strcmp(arr[i].op, "+") == 0 || strcmp(arr[i].op, "*") == 0) {
if ((strcmp(arr[i].op1, arr[j].op1) == 0 && strcmp(arr[i].op2, arr[j].op2) == 0) ||
(strcmp(arr[i].op1, arr[j].op2) == 0 && strcmp(arr[i].op2, arr[j].op1) == 0)) {
arr[j].flag = 1;
change(i, j, NULL);
}
} else {
if (strcmp(arr[i].op1, arr[j].op1) == 0 && strcmp(arr[i].op2, arr[j].op2) == 0) {
arr[j].flag = 1;
change(i, j, NULL);
}
}
}
}
}
}
void output() {
printf("\nOptimized code:\n");
for (int i = 0; i < n; i++) {
if (!arr[i].flag) {
printf("%s %s %s %s\n", arr[i].op, arr[i].op1, arr[i].op2, arr[i].res);
}
}
}
void change(int p, int q, char *res) {
for (int i = q + 1; i < n; i++) {
if (strcmp(arr[q].res, arr[i].op1) == 0) {
if (res == NULL) {
strcpy(arr[i].op1, arr[p].res);
} else {
strcpy(arr[i].op1, res);
}
} else if (strcmp(arr[q].res, arr[i].op2) == 0) {
if (res == NULL) {
strcpy(arr[i].op2, arr[p].res);
} else {
strcpy(arr[i].op2, res);
}
}
}
}
 exp 14
 #include<stdio.h>
#include<stdlib.h>
#define TRUE 1
#define FALSE 0
typedef struct Heap
{
int data;
struct Heap *next;
} node;
node *create();
void display(node *);
node *insert(node *);
void dele(node **);
int main()
{
int choice;
node *head = NULL;
do
{
printf("\nProgram to perform various operations on heap using dynamic memory management");
printf("\n1. Create");
printf("\n2. Display");
printf("\n3. Insert an element in a list");
printf("\n4. Delete an element from list");
printf("\n5. Quit");
printf("\nEnter your choice (1-5): ");
scanf("%d", &choice);
switch (choice)
{
case 1:
head = create();
break;
case 2:
display(head);
break;
case 3:
head = insert(head);
break;
case 4:
dele(&head);
break;
case 5:
exit(0);
default:
printf("Invalid choice, try again.\n");
}
} while (choice != 5);
return 0;
}
node *create()
{
node *temp, *New, *head;
int val, flag;
char ans = 'y';
temp = NULL;
flag = TRUE;
do
{
printf("\nEnter the element: ");
scanf("%d", &val);
New = (node *)malloc(sizeof(node));
if (New == NULL)
{
printf("Memory is not allocated.");
exit(1);
}
New->data = val;
if (flag == TRUE)
{
head = New;
temp = head;
flag = FALSE;
}
else
{
temp->next = New;
temp = New;
}
printf("Do you want to enter more elements? (y/n): ");
scanf(" %c", &ans);
} while (ans == 'y');
printf("\nThe list is created\n");
return head;
}
void display(node *head)
{
node *temp;
temp = head;
if (temp == NULL)
{
printf("\nThe list is empty\n");
return;
}
printf("List: ");
while (temp != NULL)
{
printf("%d -> ", temp->data);
temp = temp->next;
}
printf("NULL\n");
}
node *insert(node *head)
{
node *New;
int val;
char ans;
New = (node *)malloc(sizeof(node));
if (New == NULL)
{
printf("Memory is not allocated.");
exit(1);
}
printf("Enter the element you want to insert: ");
scanf("%d", &val);
New->data = val;
New->next = NULL;
if (head == NULL)
{
head = New;
}
else
{
New->next = head;
head = New;
}
return head;
}
void dele(node **head)
{
node *temp, *prev;
int key;
temp = *head;
if (temp == NULL)
{
printf("\nThe list is empty\n");
return;
}
printf("\nEnter the element you want to delete: ");
scanf("%d", &key);
while (temp != NULL && temp->data != key)
{
prev = temp;
temp = temp->next;
}
if (temp == NULL)
{
printf("Element not found in the list\n");
return;
}
if (temp == *head)
{
*head = (*head)->next;
}
else
{
prev->next = temp->next;
}
free(temp);
printf("\nThe element is deleted\n");
}


