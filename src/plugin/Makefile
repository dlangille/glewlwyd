#
# Glewlwyd potocol backend
#
# Makefile used to build the software
#
# Copyright 2016-2019 Nicolas Mora <mail@babelouest.org>
#
# This program is free software; you can redistribute it and/or
# modify it under the terms of the GNU Lesser General Public License
# as published by the Free Software Foundation;
# version 2.1 of the License.
#
# This library is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.	See the
# GNU GENERAL PUBLIC LICENSE for more details.
#
# You should have received a copy of the GNU General Public
# License along with this library.	If not, see <http://www.gnu.org/licenses/>.
#

DEST=/usr/local
MODULES_TARGET=$(DEST)/lib/glewlwyd/plugin
CC=gcc
CFLAGS=-c -fPIC -Wall -Werror -Wextra -Wno-unknown-pragmas -D_REENTRANT -Wno-pragmas $(shell pkg-config --cflags liborcania) $(shell pkg-config --cflags libyder) $(shell pkg-config --cflags jansson) $(shell pkg-config --cflags libhoel) $(shell pkg-config --cflags libjwt) $(shell pkg-config --cflags gnutls) $(ADDITIONALFLAGS)
LIBS=$(shell pkg-config --libs liborcania) $(shell pkg-config --libs libyder) $(shell pkg-config --libs libulfius) $(shell pkg-config --libs libhoel) $(shell pkg-config --libs jansson) -ldl

all: release

%.o: %.c ../glewlwyd-common.h
	$(CC) $(CFLAGS) $(CPPFLAGS) $<

misc.o: ../misc.c ../glewlwyd-common.h
	$(CC) $(CFLAGS) $(CPPFLAGS) ../misc.c

glewlwyd_resource.o: ../../docs/resources/ulfius/glewlwyd_resource.c ../../docs/resources/ulfius/glewlwyd_resource.h
	$(CC) $(CFLAGS) $(CPPFLAGS) ../../docs/resources/ulfius/glewlwyd_resource.c

oidc_resource.o: ../../docs/resources/ulfius/oidc_resource.c ../../docs/resources/ulfius/oidc_resource.h
	$(CC) $(CFLAGS) $(CPPFLAGS) ../../docs/resources/ulfius/oidc_resource.c

libprotocol_oauth2.so: protocol_oauth2.o misc.o glewlwyd_resource.o ../glewlwyd-common.h ../../docs/resources/ulfius/glewlwyd_resource.h
	$(CC) -shared -Wl,-soname,libprotocol_oauth2.so -o libprotocol_oauth2.so protocol_oauth2.o misc.o glewlwyd_resource.o $(LIBS) $(shell pkg-config --libs libjwt)

libprotocol_oidc.so: protocol_oidc.o misc.o oidc_resource.o ../glewlwyd-common.h ../../docs/resources/ulfius/oidc_resource.h
	$(CC) -shared -Wl,-soname,libprotocol_oidc.so -o libprotocol_oidc.so protocol_oidc.o misc.o oidc_resource.o $(LIBS) $(shell pkg-config --libs libjwt) $(shell pkg-config --libs gnutls)

libprotocol_mock.so: mock.o misc.o ../glewlwyd-common.h
	$(CC) -shared -Wl,-soname,libprotocol_mock.so -o libprotocol_mock.so mock.o misc.o $(LIBS)

clean:
	rm -f *.o *.so

debug: ADDITIONALFLAGS=-DDEBUG -g -O0

debug: libprotocol_mock.so libprotocol_oauth2.so libprotocol_oidc.so

release: ADDITIONALFLAGS=-O3

release: libprotocol_oauth2.so libprotocol_oidc.so

install:
	mkdir -p $(MODULES_TARGET)
	cp -f *.so $(MODULES_TARGET)
