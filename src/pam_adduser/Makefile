CFLAGS += -Wall -fPIC -Werror

CFLAGS += -I$(ROOTDIR)/lib/libpam/libpam/include
LDFLAGS = -nostdlib -L$(ROOTDIR)/lib

ifdef CONFIG_USER_SSH_SSH
CFLAGS += -DHAVE_SSH
endif

all: pam_adduser.so

pam_adduser.so: pam_adduser.o
	$(CC) -shared  $(LDFLAGS) -o $@ pam_adduser.o -lopengear -lpam -lexpat -lscew $(LIBS)

clean:
	@rm -f *~ *.so *.o

romfs:
	$(ROMFSINST) $(wildcard *.so)  /lib/security
