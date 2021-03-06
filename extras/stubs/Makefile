all: leyra

deps:
	{{ range $key, $value := .Goget}}go get {{ $value }}
	{{ end }}

env:
	cp env.example .env

leyra: env deps main.go
	go fmt leyra/...
	go build -v -o server

run: leyra
	@echo "Starting your application..."
	./server

.PHONY: acbuild
acbuild: leyra
	rm -f server
	CGO_ENABLED=0 GOOS=linux go build -v -o server-linux-amd64 -a -tags netgo -ldflags '-w' .
	@echo "Build successful!"
	@echo "Wrapping your application up into an ACI..."
	mkdir /tmp/acbuild
	cp manifest /tmp/acbuild/manifest
	mkdir /tmp/acbuild/rootfs
	mkdir /tmp/acbuild/rootfs/bin
	mv server-linux-amd64 /tmp/acbuild/rootfs/bin/server-linux-amd64
	mkdir /tmp/acbuild/rootfs/app
	cp -R ./* /tmp/acbuild/rootfs/app
	cp .env /tmp/acbuild/rootfs/app/.env
	mv /tmp/acbuild .
	cd acbuild && tar czf server-${VERSION}-linux-amd64.tar.gz manifest rootfs
	mv acbuild/server-${VERSION}-linux-amd64.tar.gz images/server-${VERSION}-linux-amd64.aci
	rm -rf acbuild
	@echo "ACI build was successful!"
	@echo "It can now be found in images/"

.PHONY: clean
clean: server
	rm server
