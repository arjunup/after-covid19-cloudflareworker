const html = todos => `
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bulma@0.9.3/css/bulma.min.css">
  </head>

 <body class="bg-blue-100">
  <section class="hero is-primary">
  <div class="hero-body">
    <div class="container has-text-centered">
      <p class="title">After Covid19</p>
    </div>
  </div>
  </section>

  <section class="section" style="border-bottom: 1px solid #00d1b2">
    <div class="container is-fluid">
      <h1 class="title" id="todocontainer"></h1>     
      <div id="todos"></div>
    </div>
   </section>

    <p style="border-top:1px black;"></p>
    <footer class="footer">
      <div class="content has-text-centered">
        <p>
          @after-covid19  
        </p>
      </div>
    </footer> 
  </body>

  <script>
    window.todos = ${todos}

    var populateTodos = function() {
      var todoContainer = document.querySelector("#todos")
      todoContainer.innerHTML = window.todos.userinput
      var todoTitleContainer = document.querySelector("#todocontainer")
      todoTitleContainer.innerHTML = "The user " + window.todos.username + " entered"
      console.log("output", window.todos.userinput)
    }
    
    populateTodos()

  </script>
</html>
`

const defaultData = { todos: [] }

const setCache = (key, data) => AFTER_COVID19.put(key, data)
const getCache = key => AFTER_COVID19.get(key)

async function getTodos(request) {
 
  const ip = request.headers.get('CF-Connecting-IP')
  const { searchParams } = new URL(request.url)
  let username = searchParams.get('username')

  const cacheKey = username;
  let data
  const cache = await getCache(cacheKey)
  console.log(cache)
  if (!cache) {
    await setCache(cacheKey, defaultData)
    data = defaultData
  } else {
    data = cache
  }
  console.log(data);
  const body = html(JSON.stringify(data).replace(/</g, "\\u003c"))
  return new Response(body, {
    headers: { 'Content-Type': 'text/html' },
  })
}

async function updateTodos(request) {
   const contentType = request.headers.get("content-type") || "";
   if (contentType.includes("form")) {
    const formData = await request.formData();
    const body = Object.fromEntries(formData);
    console.log(body)
    JSON.stringify(body);
    console.log(body.userinput)
   
  //const body = await request.text()
  const { searchParams } = new URL(request.url)
  let username = searchParams.get('username')
  console.log(username);
  let userinput = searchParams.get('userinput')
  console.log(userinput);

  const ip = request.headers.get("CF-Connecting-IP")
  const cacheKey = `data-${ip}`;
  try {
    //JSON.parse(username, userinput)
    await setCache(body.username, body.userinput)
    const output = html(JSON.stringify(body).replace(/</g, "\\u003c"))
  return new Response(output, {
    headers: { 'Content-Type': 'text/html' },
  })
   // return new Response(JSON.stringify(body), { status: 200 })
  } catch (err) {
    return new Response(err, { status: 500 })
  }
   }
}

async function handleRequest(request) {
  
  if (request.method === "POST") {
    return updateTodos(request);
  } else if (request.method === "GET") {
    // Defined in previous code block
    return getTodos(request);
  }
}

addEventListener("fetch", event => {
  event.respondWith(handleRequest(event.request))
})
