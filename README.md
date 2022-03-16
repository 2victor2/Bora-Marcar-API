# Bora-Marcar-API

Fake API criada com JSON-server e JSON-server-auth para fornecer e armazenar dados para a aplicação "Bora Marcar?".

## Endpoints

Ao todo a API tem 9 endpoints, girando em torno de um usuário gerenciar seus eventos, cadastrando novos e editando antigos, podendo editar também suas próprias infos de usuário, como por exemplo email e senha. Para os endpoints que precisam de autenticação é necessário enviar um bearer token no header da requisição.

A url base da API é: https://bora-marcar-api.herokuapp.com/

## Usuários

### Cadastro

```POST /register```

Utilizado para cadastrar um novo usuário no app, precisando ser enviado um nome de usuário, idade, email e senha.

*FORMATO DE REQUISIÇÃO*

```
{
"name": "vito",
"age": 20,
"email": "victor@email.com",
"password": "123456"
}
```

Caso tudo dê certo...

*FORMATO DE RESPOSTA - STATUS 201*

```
{
"accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6InZpY3RvcjJAZW1haWwuY29tIiwiaWF0IjoxNjQ3MzgyNzIyLCJleHAiOjE2NDczODYzMjIsInN1YiI6IjIifQ.9yjoesAnxNtZoJOBEquBHn4w3-NbvMuxg-CWAf_eKnM",
"user": {
	"email": "victor2@email.com",
	"name": "vito",
	"age": 20,
	"id": 2
}
}
```

Possíveis erros:

- Caso os campos email ou senha não forem informados:

_FORMATO DE RESPOSTA - STATUS 400_

`"Email and password are required"`

### Login

```POST /login```

Utilizado para logar um usuário existente no banco de dados, sendo necessário informar um email e uma senha correspondente de um usuário previamente cadastrado.

*FORMATO DE REQUISIÇÃO*

```
{
"email": "victor@email.com",
"password": "123456"
}
```

Caso tudo dê certo...

*FORMATO DE RESPOSTA - STATUS 200*

```
{
"accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6InZpY3RvckBlbWFpbC5jb20iLCJpYXQiOjE2NDczNzY4MTksImV4cCI6MTY0NzM4MDQxOSwic3ViIjoiMSJ9.w7aU0MvNofeyTMzE3pYz8EyLCthV6vvXr3rNRihXc8o",
"user": {
	"email": "victor@email.com",
	"name": "vito",
	"age": 20,
	"id": 1
}
} 
```

Possíveis erros: 

- Caso a senha esteja incorreta: 

*FORMATO DE RESPOSTA - STATUS 400*

``` "Incorrect password" ```

- Caso o email esteja incorreto: 

*FORMATO DE RESPOSTA - STATUS 400*

``` "Cannot find user" ```

### Editando perfil 

``` PATCH /users/:id ```

Utilizado para editar informações do perfil de um usuário, devendo ser informado no endpoint o id do usuário em questão e passado o token de autenticação no header. No caso foi utilizado /users/1.

*FORMATO DE REQUISIÇÃO*

``` 
{
"name": "victor"
}
 ```

Caso tudo dê certo...

*FORMATO DE RESPOSTA - STATUS 200*

``` 
{
"email": "victor@email.com",
"password": "$2a$10$qDCgsSnwtOCMVRK4CtO7hO26d9sgA9JGi19Qfi50a07guVwpJg4.G",
"name": "victor",
"age": 20,
"id": 1
}
 ```

### Listando usuários

```GET /users```

Utilizado para obter todos os usuários cadastrados.

*FORMATO DE RESPOSTA - STATUS 200*

``` 
[
{
	"email": "victor@gmail.com",
	"password": "$2a$10$q1c.LUss.bsB1LxFNeqdjuYmeEQMVUE/mKLkCJg9TuxHPxnVcyTT2",
	"name": "vito",
	"age": 20,
	"id": 1,
},
{
	"email": "anderson@gmail.com",
	"password": "$2a$10$JO6F2PCM48WeXVIYPr8KF.lWb6gKIeADaInQc7sa6zkVrl2da/u9O",
	"name": "anderson",
	"age": 22,
	"id": 2,
}
]
 ```

 Pode-se utilizar dos query params para obter um usuário específico: 

 ```GET /users?id=1```

*FORMATO DE RESPOSTA - STATUS 200*

```
[
{
	"email": "victor@gmail.com",
	"password": "$2a$10$q1c.LUss.bsB1LxFNeqdjuYmeEQMVUE/mKLkCJg9TuxHPxnVcyTT2",
	"name": "vito",
	"age": 20,
	"id": 1,
}
]
```

## Eventos

### Criação

``` POST /events ```

Utilizado para a criação de um novo evento, sendo necessário passar tanto o token de autenticação quanto a propriedade "userId" que deve se referir ao usuário criador/dono do evento.

*FORMATO DE REQUISIÇÃO*

``` 
{
"name": "evento",
"description": "social da galera na casa do anderson",
"participants": 5,
"itemsList": [{"itemName":"bebidas", "whoTakes": "vito"},{"itemName": "carne", "whoTakes": "marcelo"}, {"itemName": "sal grosso", "whoTakes": "johão"}],
"location": "casa do anderson",
"userId": 1
}
```

Caso tudo dê certo...

*FORMATO DE RESPOSTA - STATUS 201*

``` 
{
"name": "eventinho",
"description": "social da galera na casa do anderson",
"participants": 5,
"itemsList": [
	{
		"itemName": "bebidas",
		"whoTakes": "victor"
	},
	{
		"itemName": "carne",
		"whoTakes": "marcelo"
	},
	{
		"itemName": "sal grosso",
		"whoTakes": "johão"
	}
],
"location": "casa do anderson",
"userId": 1,
"id": 5
}
```

Possíveis erros: 

- Caso a propriedade "userId" não seja informada no corpo da requisição:

*FORMATO DE RESPOSTA - STATUS 403*

``` "Private resource creation: request body must have a reference to the owner id" ```

### Editando eventos 

``` PATCH /events/:id ```

Utilizado para alterar as informações de um evento, sendo necessário especificar no endpoint o id do evento que será alterado, enviar o token de autenticação no header e passar a propriedade "userId" no corpo da requisição referenciando ao dono/criador do evento. No caso foi utilizado /events/5

*FORMATO DE REQUISIÇÃO*

``` 
{
"participants": 6,
"userId": 1
} 
```

Caso tudo dê certo... 

*FORMATO DE RESPOSTA - STATUS 200*

``` 
{
"name": "eventinho",
"description": "social da galera na casa do anderson",
"participants": 6,
"itemsList": [
	{
		"itemName": "bebidas",
		"whoTakes": "victor"
	},
	{
		"itemName": "carne",
		"whoTakes": "marcelo"
	},
	{
		"itemName": "sal grosso",
		"whoTakes": "johão"
	}
],
"location": "casa do anderson",
"userId": 1,
"id": 5
}
```

### Listando eventos

``` GET /events ```

Utilizado para obter todos os eventos criados.

*FORMATO DE RESPOSTA - STATUS 200*

``` 
[
{
  "name": "eventinho",
  "description": "social da galera na casa do anderson",
  "participants": 6,
  "itemsList": [
    {
      "itemName": "bebidas",
      "whoTakes": "victor"
    },
    {
      "itemName": "carne",
      "whoTakes": "marcelo"
    },
    {
      "itemName": "sal grosso",
      "whoTakes": "johão"
    }
  ],
  "location": "casa do anderson",
  "userId": 1,
  "id": 5
},
{
  "name": "outro eventinho",
  "description": "social da galera na casa do yorran",
  "participants": 3,
  "itemsList": [
    {
      "itemName": "bebidas",
      "whoTakes": "victor"
    },
    {
      "itemName": "carne",
      "whoTakes": "marcelo"
    },
    {
      "itemName": "sal grosso",
      "whoTakes": "johão"
    }
  ],
  "location": "casa do yorran",
  "userId": 2,
  "id": 6
}
]
```

Pode-se utilizar dos query params para obter um evento específico: 

``` GET /events?id=6 ```

*FORMATO DE RESPOSTA - STATUS 200*

``` 
[
{
  "name": "outro eventinho",
  "description": "social da galera na casa do yorran",
  "participants": 3,
  "itemsList": [
    {
      "itemName": "bebidas",
      "whoTakes": "victor"
    },
    {
      "itemName": "carne",
      "whoTakes": "marcelo"
    },
    {
      "itemName": "sal grosso",
      "whoTakes": "johão"
    }
  ],
  "location": "casa do yorran",
  "userId": 2,
  "id": 6
}
]
```

Ou todos os eventos de um usuário específico: 

``` GET /events?userId=1 ```

*FORMATO DE RESPOSTA - STATUS 200*

``` 
[
{
  "name": "eventinho",
  "description": "social da galera na casa do anderson",
  "participants": 6,
  "itemsList": [
    {
      "itemName": "bebidas",
      "whoTakes": "victor"
    },
    {
      "itemName": "carne",
      "whoTakes": "marcelo"
    },
    {
      "itemName": "sal grosso",
      "whoTakes": "johão"
    }
  ],
  "location": "casa do anderson",
  "userId": 1,
  "id": 5
}
] 
```

