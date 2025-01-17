## Overview

The example showcases a configurable web page to PDF conversion server.

**The problem**

The biggest challenge is that `wkhtmltopdf` can only run on the `main
thread`, the problem being that the `HTTP server` runs each request in
a separate `goroutine`.

For more information, see:  
• [https://github.com/wkhtmltopdf/wkhtmltopdf/issues/1711](https://github.com/wkhtmltopdf/wkhtmltopdf/issues/1711)  
• [https://forum.dlang.org/thread/qmnmirbwmrvwexaewmtw@forum.dlang.org](https://forum.dlang.org/thread/qmnmirbwmrvwexaewmtw@forum.dlang.org)  
• [https://forum.qt.io/topic/84485/qapplication-not-created-in-main-thread/9](https://forum.qt.io/topic/84485/qapplication-not-created-in-main-thread/9)

**The solution**

The solution is to run the conversion process on the `main thread`. This can
be achieved as shown [here](https://github.com/golang/go/wiki/LockOSThread).
There are also a couple of packages which facilitate this, like
[faiface/mainthread](https://github.com/faiface/mainthread) or
[olahol/mainthread](https://github.com/olahol/mainthread). The example does
not use any external library to access the `main thread`.

## Usage

**Run the conversion server.**

```bash
go run main.go
```

**Make a conversion request.**

```bash
curl -H "Content-Type: application/json" -X POST \
    -o google.pdf 127.0.0.1:8080 \
    -d'{
	"converterOpts": {
		"title": "google.com",
		"paperSize": "A4",
		"orientation": "Portrait"
	},
	"objectOpts": {
		"location": "https://google.com",
		"footer": {
			"contentCenter": "[page]"
		}
	}
}'
```

See full list of options at [https://pkg.go.dev/github.com/litesrv/go-wkhtmltopdf](https://pkg.go.dev/github.com/litesrv/go-wkhtmltopdf).
