# Copyright 2020 Kungliga Tekniska högskolan

# Permission is hereby granted, free of charge, to any person
# obtaining a copy of this software and associated documentation files
# (the "Software"), to deal in the Software without restriction,
# including without limitation the rights to use, copy, modify, merge,
# publish, distribute, sublicense, and/or sell copies of the Software,
# and to permit persons to whom the Software is furnished to do so,
# subject to the following conditions:

# The above copyright notice and this permission notice shall be
# included in all copies or substantial portions of the Software.

# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
# EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
# MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
# NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS
# BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN
# ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
# CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

FROM centos:8 AS fetchverify

RUN mkdir /src
ADD http://apache.mirrors.spacedump.net/hadoop/common/hadoop-3.3.0/hadoop-3.3.0-src.tar.gz /src/
RUN sha512sum /src/hadoop-3.3.0-src.tar.gz | fgrep 3363782625e28402947c6a0bd5dddcc3481c8691ba59cbb369634bc5b1ae610e1eed9b542ce151c86ec8faa69df90fb53f35c2e4133058075e53728a22800f3d

FROM scratch
COPY --from=fetchverify /src/hadoop-3.3.0-src.tar.gz /
