
foo := a.o b.o cc.o
bar1 := $(foo:.o=.c) #bar是a.c b.c cc.c
#或者更加精确一些
bar2 := $(foo:%.o=%.c)#bar1是a.c b.c cc.o

hello: 
	echo $(bar1)
	echo $(bar2)

