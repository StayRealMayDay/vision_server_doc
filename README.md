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
