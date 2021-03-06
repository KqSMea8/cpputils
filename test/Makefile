CC = g++
CPPFLAGS = -D__STDC_LIMIT_MACROS -m64 -Wno-deprecated -Wall -g -ggdb

UTILS_DIR=./../src/utils
CLIENT_DIR=./../src/client
INC_DIR=-I${UTILS_DIR} -lpcre -lm -lcurl -lpthread

utils_objs=${UTILS_DIR}/strutils.o \
           ${UTILS_DIR}/rune_utils.o \
           ${UTILS_DIR}/md5.o \
           ${UTILS_DIR}/some.o \
           ${UTILS_DIR}/color.o \
           ${UTILS_DIR}/regexp.o \
           ${UTILS_DIR}/terminal_table.o \

client_objs=${CLIENT_DIR}/http_client_utils.o \
            ${CLIENT_DIR}/curl_handle.o

all_objs=${utils_objs} ${client_objs}

%.o:%.cpp 
	$(CC) $(CFLAGS) ${INC_DIR} -c $< -o $@

all: str.test \
     regex.test \
     reg_replace_callback.test \
     rune.test \
     table.test \
     curl.test

#在编译curl.test时，-lcurl放到了curl.cpp后面就提示“对‘curl_easy_init’未定义的引用”这样的错误，调整一下curl.cpp跟-lcurl的顺序即可！
#如果-l指定的库出现的位置在引用它的对象文件之前出现那么会出现相应的函数找不到（无法加载的问题）。所以-l的顺序是很重要的。man文档中有说明。
#
#     It makes a difference where in the command you write this option;
#            the linker searches and processes libraries and object files in the
#            order they are specified.  Thus, foo.o -lz bar.o searches library z
#            after file foo.o but before bar.o.  If bar.o refers to functions in
#            z, those functions may not be loaded.
#
#符号解析阶段，链接器从左到右按照输入的顺序来扫描可重定位目标文件和存档文件。扫描的时候，链接器维持三个集合：
#1. 可重定位目标文件集合E
#2. 未解析的符号集合U
#3. 已定义的符号集合D
#
#初始时三个集合都为空。算法：
#1. 对于命令行上每个文件f，如果f是目标文件，链接器将f添加到E， 然后修改U和D来反映f中的符号定义和引用，并继续读入下一个输入文件；
#2. 如果f是存档文件，那么链接器就尝试匹配U中未解析的符号和由存档文件成员定义的符号。如果某个存档文件成员m，定义了一个符号来解析U中的一个引用，那么将m加入到E中， 并且链接器修改U和D来反映m中的符号定义和引用。对存档文件中所有的成员目标文件反复进行这个过程，直到U和D都不再发生变化。在此时，任何不包含在E中的成员目标文件都被简单丢弃，而链接器将继续处理下一个输入文件；
#3. 链接器完成对命令行上输入文件的扫描后， U非空的话，那么链接器输出一个错误并终止。否则，它会合并和重定位E中的目标文件，从而构建可执行文件。

str.test: str.o ${all_objs}
	${CC} ${all_objs} -o str.test ${CPPFLAGS} str.cpp ${INC_DIR}

regex.test: regex.o ${all_objs}
	${CC} ${all_objs} -o regex.test ${CPPFLAGS} regex.cpp ${INC_DIR}

reg_replace_callback.test: reg_replace_callback.o ${all_objs}
	${CC} ${all_objs} -o reg_replace_callback.test ${CPPFLAGS} reg_replace_callback.cpp ${INC_DIR}

rune.test: rune.o ${all_objs}
	${CC} ${all_objs} -o rune.test ${CPPFLAGS} rune.cpp ${INC_DIR}

table.test: table.o ${all_objs}
	${CC} ${all_objs} -o table.test ${CPPFLAGS} table.cpp ${INC_DIR}

curl.test: curl.o ${all_objs}
	${CC} ${all_objs} -o curl.test ${CPPFLAGS} curl.cpp ${INC_DIR}

clean:
	rm -rf ${all_objs}
	rm -rf *.test *.o *.dSYM
	rm -rf core*
