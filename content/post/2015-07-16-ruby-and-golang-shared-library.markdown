+++
date = "2015-07-16T13:03:04+08:00"
draft = false
title = "使用 Golang 的 Shared Library 來幫 Ruby 專案加速"

+++


Golang 日前釋放出了 1.5 Beta 1, 已經可以將 Golang 的程式編成 shared library 來給其他程式呼叫

相關的範例可以參考 http://qiita.com/yanolab/items/1e0dd7fd27f19f697285

不過這該文章中是使用 python 呼叫 golang 的程式

如果是用 ruby 的話, 可以透過 ffi https://github.com/ffi/ffi 來做到

寫起來大致上是

```
require 'ffi'
require 'benchmark'

module LibGo
  extend FFI::Library
  ffi_lib './libgo.so'
  attach_function :fib, [:int], :int

end

def fib(i)
  return i if i < 2
  return fib(i-2)+fib(i-1)
end

n=10000
Benchmark.bm do |x|
  x.report{ n.times{ fib(16); }}
  x.report{ n.times{ LibGo.fib(16) }}
end
```

Benchmark 的結果則是快了將近 20 倍

```
ser     system      total        real
1.130000   0.000000   1.130000 (  1.132376)
0.060000   0.000000   0.060000 (  0.068342)
```

這顯示如果拿 golang 來幫 ruby 程式中的演算法加速應該可以取得不小的效果

接下來看另一個例子, 從 Accept-Language 中挑出適合的語言


Golang 的部份

```
package main

import (
    "C"
    "golang.org/x/text/language"
    "log"
)

var (
    mather = language.NewMatcher([]language.Tag{language.Make("en"), language.Make("ja"), language.Make("zh-TW"), language.Make("zh-CN")
})
)

//export preferredLanguageFrom
func preferredLanguageFrom(httpAcceptLanguage *string) *string {

    tag, _, _ := language.ParseAcceptLanguage(*httpAcceptLanguage)
    t, _, _ := mather.Match(tag...)
    l := t.String()
    return &l
}

//export preferredLanguageFromUseCString
func preferredLanguageFromUseCString(cHttpAcceptLanguage *C.char) *C.char {
    httpAcceptLanguage := C.GoString(cHttpAcceptLanguage)

    tag, _, _ := language.ParseAcceptLanguage(httpAcceptLanguage)
    t, _, _ := mather.Match(tag...)
    return C.CString(t.String())
}

func init() {
    log.Println("Loaded!!")
}

func main() {
}
```


Ruby 的部份

```
require 'ffi'
require 'benchmark'

# gem 'http_accept_language'
require 'http_accept_language/parser'

module LibGo

  class GoString < FFI::Struct
    layout :p, :pointer,
      :n, :int
  end

  extend FFI::Library
  ffi_lib './libgo.so'

  attach_function :preferredLanguageFrom, [GoString.by_ref], GoString.by_ref
  attach_function :preferredLanguageFromUseCString, [:string], :string
end

@lang = "en;q=0.9,zh;q=0.8,en-US;q=0.6,en-GB;q=0.2"
@availables = %w(en ja zh-CN zh-TW )

def use_go
  x = LibGo::GoString.new
  x[:p] = FFI::MemoryPointer.from_string(@lang)
  x[:n] = @lang.length
  result =  LibGo.preferredLanguageFrom( x )
  result[:p].get_string(0, result[:n])
end

def use_go_cstring
  LibGo.preferredLanguageFromUseCString( @lang )
end

def use_ruby
  parser = HttpAcceptLanguage::Parser.new(@lang)
  parser.preferred_language_from( @availables )
end

n=100000

Benchmark.bm do |x|
  x.report{ n.times{ use_go() }}
  x.report{ n.times{ use_go_cstring() }}
  x.report{ n.times{ use_ruby() }}
end
```

跑出來的結果

```
       user     system      total        real
   1.210000   0.030000   1.240000 (  1.111699)
   0.960000   0.050000   1.010000 (  0.885242)
   1.400000   0.000000   1.400000 (  1.399969)

```

在這個例子裡面 golang 依然比 ruby 快了不少,
不過可以看到用 GoString 這個 struct 的時候會花掉比較多的時間
推測是 ruby 中把字串轉成 struct 的時候比較慢, 所以改用 CString 來傳遞的話就還可以壓榨出額外的效能



