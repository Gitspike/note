# 头文件`ctype.h`

**以下函数均为宏定义，并非真正的函数**

* `int isalnum(int c)`:检查参数c是字母或数字，是返回TRUE，否则返回FALSE
* `int isalpha (int c)`:检查是否为英文字母
* `int isascii(int c)`：检查是否是ASCII字符
* `int iscntrl(int c)`:检查参数 c 是否为 ASCII 控制码，也就是判断 c 的范围是否在 0 到 30 之间
* `int isdigit(int c)`:检查参数 c 是否为阿拉伯数字 0 到 9
* `int isgraph (int c) `:检查参数 c 是否为可打印字符，若 c 所对映的 ASCII 码可打印，且 非空格字符则返回 TRUE，否则返回NULL，同`int isprint(int c)`
* `int islower(int c)`:顾名思义
* `int isspace(int c)`:检查参数 c 是否为空格字符，也就是判断是否为空格（’’）、定位字 符（’\t’）、CR（’\r’）、换行（’\n’）、垂直定位字符（’\v’）或翻页（’\f’） 的情况。
* `int ispunct (int c) `:检查参数 c 是否为标点符号或特殊符号。返回 TRUE 也就是代表参 数 c 为非空格、非数字和非英文字母。 若参数 c 为标点符号或特殊符号，则返回 TRUE，否则返回 NULL （0）
* `int isupper(int c)`:
* `int isxdigit(int c)`:检查参数 c 是否为 16 进制数字,若参数 c 为 16 进制数字，则返回 TRUE，否则返回 NULL（0）

# 头文件`stdlib.h`

* `double atof(const char *nptr)`:atof（）会扫描参数 nptr 字符串，跳过前面的空格字符，直到遇上 数字或正负符号才开始做转换，而再遇到非数字或字符串结束时 （’\0’）才结束转换，并将结果返回。参数 nptr 字符串可包含正负号、小数点或 Ehexo（e）来表示指数部分，如 123.456 或 123e-2
* `int atoi(const char *nptr)`:atoi()会扫描参数 nptr 字符串，跳过前面的空格字符，直到遇上数字 或正负符号才开始做转换，而再遇到非数字或字符串结束时（’\0’） 才结束转换，并将结果返回
* `long atol(const char *nptr)`:atol()会扫描参数 nptr 字符串，跳过前面的空格字符，直到遇上数字 或正负符号才开始做转换，而再遇到非数字或字符串结束时（’\0’） 才结束转换，并将结果返回
* `char *gcvt(double number，size_t ndigits，char *buf)`:
  * 将浮点型数转换为字符串(ASCII)，取四舍五入
  * ndigits 表示 显示的位数
  * 若转换成功，转换后的字符串 会放在参数 buf 指针所指的空间
  * 返回一字符串指针，此地址即为 buf 指针
* `double strtod(const char *nptr,char **endptr)`
* 