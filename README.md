# Introduction

+[![Circle CI](https://circleci.com/gh/phuongdo/go-redisproto.svg?style=svg&circle-token=f008be1b378dbf91d68b4b43e0cd2cab97698afe)](https://circleci.com/gh/phuongdo/go-redisproto)

go-redisproto is a go library to create server, service for RPC that compatible with redis protocol
I use it for some projects that require RPC, redis-protocol is a good choice because it can be parsed fast and
we have many client libraries that already exist to use. go-redisproto use it's own buffered reader to avoid memory copy.

Some other tool that use `go-redisproto`

1. https://github.com/secmask/mqueue    
2. https://github.com/secmask/roller

# License
go-redisproto is available under The MIT License (MIT).
