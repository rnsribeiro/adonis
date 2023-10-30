tags: #nodejs #javascript #web #back-end #programação 

Video base: [# API RESTful com AdonisJS - Aprenda Adonis em 1 hora](https://www.youtube.com/watch?v=y8XfJJYhXPE&ab_channel=MatheusBattisti-HoradeCodar)

1. Para inicializar o projeto:
```bash
npm init adonis-ts-app@latest .
```

2. Comando para rodar o projeto
```bash
node ace serve --watch
```

3. Instalar o `ORM lucid` para trabalhar com banco de dados
```bash
npm i @adonisjs/lucid
```

4. Depois de instalado o `lucid` configurar qual banco de dados será usado na aplicação
```bash
node ace configure @adonisjs/lucid
```

5. Select where to display instructions
 ```bash
 In the terminal
 ```
 
6. Encontrar o arquivo `cors.ts` e alterar a linha:
```javascript
- enable: false,
+ enabled: (request) => request.url().startsWith('/api'),
```

7. Para se criar um `model`o que seria como se fosse uma tabela, no caso um `model` chamado `Moment`  com a opção `-m` que automaticamente já cria uma `migration` 
```bash
node ace make:model Moment -m
```

8. Adicione as linhas ao arquivo `Models/Moment.ts` 
```javascript
import { DateTime } from 'luxon'
import { BaseModel, column } from '@ioc:Adonis/Lucid/Orm'

export default class Moment extends BaseModel {
	@column({ isPrimary: true })
	public id: number  
	
+	@column()
+	public title: string
	
+	@column()
+	public description: string
	
+	@column()
+	public image: string
	
	@column.dateTime({ autoCreate: true })
	public createdAt: DateTime
	
	@column.dateTime({ autoCreate: true, autoUpdate: true })
	public updatedAt: DateTime
}
```

9. E também adicione as seguintes linhas ao aquivo `*_moments.ts` 
```javascript
import BaseSchema from '@ioc:Adonis/Lucid/Schema'

export default class extends BaseSchema {
  protected tableName = 'moments'

  public async up() {
    this.schema.createTable(this.tableName, (table) => {
      table.increments('id')
+     table.string('title')
+     table.string('description')
+     table.string('image')

      /**
       * Uses timestamptz for PostgreSQL and DATETIME2 for MSSQL
       */
      table.timestamp('created_at', { useTz: true })
      table.timestamp('updated_at', { useTz: true })
    })
  }

  public async down() {
    this.schema.dropTable(this.tableName)
  }
}
```


10. Para rodar as `migration`  que estão pendentes execute o comando:
```bash
node ace migration:run
```

11. Para visualizar a base de dados criada utilize a extensão no vscode `sqLite Viewer`

12. O próximo passo é criar o `Controller`  o qual será o responsável por gerenciar as ações. Para se criar um `Controller`  siga o seguinte passo:
```bash
node ace make:controller Moment
```

13. Edite o arquivo `Controller`  criado como `Controller/Http/MomentsController.ts
```javascript
// import type { HttpContextContract } from '@ioc:Adonis/Core/HttpContext'

export default class MomentsController {
+    public async store()  {
+        return {
+            msg: 'Deu certo!',
+        }
+   }
}
```

14. E o arquivo `start/routes.ts`
```javascript
import Route from '@ioc:Adonis/Core/Route'

Route.group(() => {
  Route.get('/', async () => {
    return { hello: 'world' }
  })

+ Route.post('/moments', 'MomentsController.store')
}).prefix('/api')
```

15. Para listar as rotas disponíveis execute o seguinte comando:
```bash
node ace list:routes
```

16. As rotas disponíveis são exibidas:
```bash
GET|HEAD    /uploads/* ─────────────────────────── drive.local.serve › Closure
GET|HEAD    /api ───────────────────────────────────────────────────── Closure
POST        /api/moments ───────────────────────────── MomentsController.store
```

17. Mas se alterar o arquivo `start/routes.ts` para:
```javascript
import Route from '@ioc:Adonis/Core/Route'

Route.group(() => {
  Route.get('/', async () => {
    return { hello: 'world' }
  })

-  Route.post('/moments', 'MomentsController.store')
+  Route.resource('/moments', 'MomentsController')
}).prefix('/api')
```

18. Executar o comando do passo `15`  você terá a seguinte saída:
```
GET|HEAD     /uploads/* ────────────────────────── drive.local.serve › Closure
GET|HEAD     /api ──────────────────────────────────────────────────── Closure
GET|HEAD     /api/moments ──────────── moments.index › MomentsController.index
GET|HEAD     /api/moments/create ─── moments.create › MomentsController.create
POST         /api/moments ──────────── moments.store › MomentsController.store
GET|HEAD     /api/moments/:id ────────── moments.show › MomentsController.show
GET|HEAD     /api/moments/:id/edit ───── moments.edit › MomentsController.edit
PUT|PATCH    /api/moments/:id ────── moments.update › MomentsController.update
DELETE       /api/moments/:id ─────moments.destroy › MomentsController.destroy
```

Perceba que foram criados novas rotas automaticamente.

19. Mas para trazer apenas as rotas de `API` para o `controller`  altere o arquivo `start/routes.ts`  para:
```javascript
import Route from '@ioc:Adonis/Core/Route'

Route.group(() => {
  Route.get('/', async () => {
    return { hello: 'world' }
  })

-  Route.post('/moments', 'MomentsController')
+  Route.resource('/moments', 'MomentsController').apiOnly()
}).prefix('/api')
```

20. Novamente com a execução do comando do passo `15` receberá a seguinte saída:
```

GET|HEAD     /uploads/* ────────────────────────── drive.local.serve › Closure
GET|HEAD     /api ──────────────────────────────────────────────────── Closure
GET|HEAD     /api/moments ──────────── moments.index › MomentsController.index
POST         /api/moments ──────────── moments.store › MomentsController.store
GET|HEAD     /api/moments/:id ────────── moments.show › MomentsController.show
PUT|PATCH    /api/moments/:id ────── moments.update › MomentsController.update
DELETE       /api/moments/:id ──── moments.destroy › MomentsController.destroy
```

21. Para realizar upload de arquivos do tipo imagem, necessário instalar o pacote `uuid` para ser utilizado para gerar nomes aleatórios e únicos no sistema:
```bash
npm i uuid
```
