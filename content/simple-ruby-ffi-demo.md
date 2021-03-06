+++

Categories = ["ruby", "ruby-ffi"]
Description = "a simple ruby-ffi demo"
date = "2015-12-08"
menu = "main"
title = "Simple ruby-ffi Demo by yang"

+++

## Simple ruby-ffi Demo

It is a demo use ruby-ffi to call c code. Only tested it on ubuntu(15.04). The `libcal` just calculate the sum of two integer number. You can see all of the code [here](https://github.com/FlowerWrong/ffi-demos).

#### Steps

1. write the `cal` lib code
2. generate .so shared lib
3. write test for this .so shared lib
4. write ruby file with ruby-ffi

#### Generate .so shared lib

```c
// cal.h
int sum(int a, int b);


// cal.c
#include "cal.h"

int sum(int a, int b) {
  return a + b;
}
```

```bash
gcc -fPIC -shared -o libcal.so cal.c
sudo mv cal.h /usr/local/include
sudo mv libcal.so /usr/local/lib
sudo chmod 0755 /usr/local/lib/libcal.so

sudo ldconfig
ldconfig -p | grep cal
```

#### Test it in main function

```c
// main.c
#include <stdio.h>
#include "cal.h"

int main(int argc, char const *argv[]) {
  int total;
  total = sum(1, 4);
  printf("sum is %d\n", total);
  return 0;
}
```

```bash
gcc -Wall -o main main.c -I/usr/local/include -L/usr/local/lib -lcal
ldd main
./main
```

#### Ruby ffi

```bash
gem install ffi
```

```ruby
require 'ffi'

module MyCal
  extend FFI::Library
  ffi_lib 'c' # use libc
  attach_function :puts, [:string], :int # puts function is from libc

  ffi_lib 'cal' # use libcal write by myself
  attach_function :sum, [:int, :int], :int # sum function is from libcal
end

MyCal.puts 'I am using libcal write by myself.'
total = MyCal.sum(1, 3)
p "sum 1 and 3 is #{total}"
```

```bash
ruby cal.rb # => "sum 1 and 3 is 4"
```

#### Todo

- [ ] [write a http-parser ffi](https://github.com/nodejs/http-parser)

#### [ruby-ffi demo repo](https://github.com/FlowerWrong/ffi-demos)

#### Reference

* [shared-libraries-linux-gcc](http://www.cprogramming.com/tutorial/shared-libraries-linux-gcc.html)
* [ruby-ffi wiki](https://github.com/ffi/ffi/wiki)
