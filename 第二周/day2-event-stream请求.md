## 后端格式event-strem
    1、后端格式，res.setHeader('Content-Type', 'text/event-stream');
    2、res.setHeader('Cache-Control', 'no-cache'); 不能缓存
    3、res.setHeader('Connection', 'keep-alive'); 保持http1.1长连接
    4、消息发送：采用res.write(`data: ${JSON.stringify({ error: error.message })}\n\n`)，而不能用send方法
    5、结束请求： type:done,res.write(`data: ${JSON.stringify({ type: 'done' })}\n\n`); res.end();

## 前端请求
    1、请求方法：const eventSource = new EventSource（'get请求接口'）
    2、消息接收：eventSource.onmessage = (event) => { const data = JSON.parse(event.data); })
    3、结束接收：eventSource.close();
