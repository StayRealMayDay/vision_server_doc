# vision erver doc

### 单个 prompt 请求接口

URL：**/api/completion/single-prompt-request**

Method: POST

Request parameters

```
{
    model: IModel;
    params: IParams;
}
// 不同模型与其对应参数
type IModel = "text-davinci-003" | "text-davinci-002"
type IParams = {
    prompt: string | string[]; // 提示语
    suffix?: string;       // 提示语的后缀
    max_tokens?: number;    // 生成内容的最大token数量
    temperature?: number; // value [0, 2] //参数，数值越高随机性越高， 和top_p不同时使用
    top_p?: number; // [0, 1] // 0.1表示只考虑前面10%的token。
    n?: number; //  how many result you want to generate.now fixed 1.
    presence_penalty?: number // [-2, 2] // 正数会使得模型趋向于谈论新主题。
    frequency_penalty?: number // [-2, 2] // 正数会降低模型生重复token的可能性。
    best_of?: number // integer  bigger than n。 生成多个，返回可能性最高的一个。
}


//
type IModel = "gpt-3.5-turbo"
type IParams = {
    messages: IMessage[];
    suffix?: string;
    max_tokens?: number;
    temperature?: number; // value [0, 2]
    top_p?: number; // [0, 1]
    n?: number; //  how many result you want to generate.
    presence_penalty?: number // [-2, 2]
    frequency_penalty?: number // [-2, 2]
    best_of?: number // integer  bigger than n
}
type IMessage = {
    role: "user" | "assistant" | "system",
    content: string;
}
```

Response

```
{
    code: number;
    data: {
        result: {
            role: "user" | "assistant" | "system",
            content: string
        }
    };
    errorMessage: string;
}
```

### 根据配置生成内容接口

URL **/api/completion/generate**

Method: POST

Request params

```
{
    promptList: IPrompt[]
}


type IPrompt = {
    id: string;
    mid: "gpt-3.5-turbo" | "text-davinci-003" | "text-davinci-002",
    prompt: string;
    dependenceIdList?: string[],
    params: {}
}
```

Response data

```
{
    statusCode: number;
    message: string;
    data: {
        missionId: string
    }
}

```

### 任务结果查询接口

URL: /api/completion/mission-query
GET

Request params

```
{
    missionId: string
}
```

Response data

```
{
    statusCode: number;
    message: string;
    data: IPromptResult[]
}

type IPromptResult = {
    id: string; // 与请求参数中promptList中每一条的id一一对应
    content: string; // 每一条prompt对应的结果
}
```

error response

```
//任务还未完成
{
	"statusCode": 1100,
	"message": "mission not finished yet."
}

//参数错误，任务id错误

{
    "statusCode": 400,
	"message": "Bad Request"
}
```

## 认证

需要在请求头带上 Authorization 字段

```
{
    Authorization: "Vision chat"
}
```

## stream 任务生成

URL: /api/completion/st/generate

Request params

```
{
    "promptList": [
        {
            "mid": "gpt-3.5-turbo",
            "id": "50561a82-aa00-41c8-8091-1e50e22fb5f2",
            "prompt": "\"你是谁\""
        }
    ]
}
```

Response data

```

// id是对应node节点的id.

{
    "id":"2","object":"text_completion","created":1683214696,"choices":[{"text":"","index":0,"logprobs":null,"finish_reason":"stop"}],"model":"text-davinci-003"
}

// 每一个节点结束时，会返回一个如下数据告知某一个节点已经结束
{"id":"2","statue":"[DONE]"}

// 当任务结束时，会返回如下数据告知任务已经结束。
{"missionId":"xxx","statue":"[FINISHED]"}

```

## 创建订单

URL: api/orders/create-order

Method: POST

Request params

```
{
    type: "tecent-pay" | "alipay"
    price: number; // 单价
    totalPrice: number;    // 总价
    uid: string;   // 用户id
    amount: number; // 总价 单位是分 100  = 1块钱
    productId: string; // 商品id
    promoCode: string; //优惠码
    productLine: string; // 产品线
    description: string; // 描述， 扫码付款的时候看到的描述
    ext?: string;    // 预留字段
}
```

Response

```
{
    statusCode: number;
    message: string;
    data:  {
        url: string; // 二维码url
    };
}
```

## 单个订单查询

URL: api/orders/query-order-by-id

Method: GET

Request params

```
{
    orderId: string;
}
```

Response

```
{
    statusCode: number;
    message: string;
    data:  	{
				"id": 13,
				"uid": "xxxx",
				"orderId": "ff8c68aeceb34401bf2fa8c58ab02a20",
				"price": 100,
				"totalPrice": 12000,
				"amount": 12,
				"promoCode": "",
				"productId": "abc",
				"status": 0,
				"productLine": "test",
				"updateTime": "2023-06-06T14:39:05.993Z",
				"createTime": "2023-06-06T14:39:05.993Z"
			}
}
```

## 多订单查询

URL: /api/orders/query-order-list

Method: GEt

Request params

```
{
    page: number;
    pageSize: number;
    uid: string;  // 用户id
    productLine: string; // 产品线
}
```

Response

```
{
	"data": {
		"list": [
			{
				"id": 13,
				"uid": "xxxx",
				"orderId": "ff8c68aeceb34401bf2fa8c58ab02a20",
				"price": 100,
				"totalPrice": 12000,
				"amount": 12,
				"promoCode": "",
				"productId": "abc",
				"status": 0,
				"productLine": "test",
				"updateTime": "2023-06-06T14:39:05.993Z",
				"createTime": "2023-06-06T14:39:05.993Z"
			}
		],
		"total": 1
	},
	"statusCode": 0,
	"errorMessage": ""
}
```

## 接受订单回调 权限开通 光速写作

URL: /api/gsxz/payment/notify

Method: POST

Request params

```
{
    uid: string;
    productId: string;
    amount: number;
}
```
