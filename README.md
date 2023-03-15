# vision erver doc

### 单个 prompt 请求接口

URL：**/api/web/completion/single-prompt-request**

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
    prompt: string | string[];
    suffix?: string;
    max_tokens?: number;
    temperature?: number; // value [0, 2]
    top_p?: number; // [0, 1]
    n?: number; //  how many result you want to generate.
    presence_penalty?: number // [-2, 2]
    frequency_penalty?: number // [-2, 2]
    best_of?: number // integer  bigger than n
    logit_bias？: Map<string, string>
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
    logit_bias？: Map<string, string>
}
type IMessage = {
    role: "user" | "assistant" | "ststem",
    content: string;
}
```

Response

```
{
    code: number;
    data: {
        result: string
    };
    errorMessage: string;
}
```
