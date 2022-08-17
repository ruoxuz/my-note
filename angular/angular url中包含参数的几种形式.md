# angular url中包含参数的几种形式

## 1. app-routing.module.ts里定义的变量

        {path: 'pay/result/:type', component: PayResultComponent, canActivate: [AuthGuard]},

赋值方式：

    https://dev.zhangshanggou.jp/pay/result/wechat

取值方式:

    constructor(
        private activatedRoute: ActivatedRoute
    ) { }
    this.activatedRoute.snapshot.paramMap.get("type")

## 2. ;号区隔的变量

    https://zhangshanggou.jp/pay/wechat;qrcode=weixin:%2F%2Fwxpay%2Fbizpayurl%3Fpr%3DkVrSjUpzz;pid=33560251;ta=201

赋值方式：

    constructor(
        private router: Router
    ) {}
    this.router.navigate(['/pay/wechat',
                {qrcode: data.get('qrcode'), pid: data.get('pid'), ta: data.get('ta')}]);

取值方式:

    constructor(
        private activatedRoute: ActivatedRoute
    ) { }
    this.activatedRoute.params.subscribe((data) => {
        if (data.qrcode != null){
          this.qrcode = <string>data.qrcode;
          this.price = data.ta;
        }
    });

## 3. ？号和&号区隔的变量

    https://zhangshanggou.jp/pay/result/wechat?pid=2005128&rst=1&ap=2005128&ec=ER000000000&sod=オーダー番号 &ta=1500&job=REQUEST&pod1=1&qrcode=weixin://wxpay/bizpayurl?pr=uwK017A00&dat1=test1

赋值方式：

取值方式:

    this.activatedRoute.queryParams.subscribe(params => {
        if (params.result !== undefined && params.result === '1') {
        this.paySuccess = true;
        }
    });

### URLSearchParams()

    // Retrieve params via url.search, passed into ctor
    const url = new URL('https://example.com?foo=1&bar=2');
    const params = new URLSearchParams(url.search);

    // Pass in a string literal
    const params2 = new URLSearchParams("foo=1&bar=2");
    const params2a = new URLSearchParams("?foo=1&bar=2");

    // Pass in a sequence of pairs
    const params3 = new URLSearchParams([["foo", "1"], ["bar", "2"]]);

    // Pass in a record
    const params4 = new URLSearchParams({"foo": "1", "bar": "2"});

[URLSearchParams()](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams/URLSearchParams)
