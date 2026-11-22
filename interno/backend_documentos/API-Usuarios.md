# Serviço de Usuários (US9)

**Base URL:** `http://localhost:8082/api/usuarios`</br>
**Autenticação:** header `Authorization: Bearer <token JWT>` (token obtido em `POST /api/auth/login`, validade de 8 h)</br>
**Permissão:** todas as rotas exigem perfil **GERÊNCIA**. Operacional recebe **403**.</br>
**Swagger:** `http://localhost:8082/swagger-ui.html`</br>

## Rotas

| Método | Rota | Descrição | Sucesso |
|---|---|---|---|
| GET | `/api/usuarios` | Lista usuários | 200 |
| GET | `/api/usuarios/{id}` | Busca por ID | 200 |
| POST | `/api/usuarios` | Cria usuário | 201 |
| PUT | `/api/usuarios/{id}` | Edita usuário | 200 |
| PATCH | `/api/usuarios/{id}/desativar` | Desativa (não exclui) | 200 |
| PATCH | `/api/usuarios/{id}/ativar` | Reativa | 200 |

## Corpo de criação (POST)

```json
{
  "nome": "João Operador",
  "email": "joao@janosys.com",
  "senha": "senha12345",
  "login": "joao",
  "perfil": "OPERACIONAL",
  "papel": "SAC"
}
```

## Corpo de edição (PUT)

Mesmos campos do POST. O campo `senha` é **opcional**: se ausente ou vazio, a senha atual é mantida.

## Resposta (usuário)

```json
{
  "id": 2,
  "nome": "João Operador",
  "email": "joao@janosys.com",
  "login": "joao",
  "perfil": "OPERACIONAL",
  "papel": "SAC",
  "ativo": true,
  "primario": false
}
```

A senha nunca é retornada.

## Perfis e papéis válidos

| Perfil | Papéis permitidos |
|---|---|
| GERENCIA | GERENTE_OPERACIONAL, LIDER |
| OPERACIONAL | OPERADOR_LOGISTICO, SAC |

## Regras de validação

| Campo | Regra |
|---|---|
| nome | Obrigatório, até 100 caracteres |
| email | Obrigatório, formato válido, único, até 150 caracteres |
| senha | Obrigatória na criação, de 8 a 72 caracteres |
| login | Obrigatório, único, até 50 caracteres |
| perfil / papel | Obrigatórios; o papel deve pertencer ao perfil |

## Códigos de erro e mensagens

| Código | Situação | Mensagem |
|---|---|---|
| 400 | Papel incompatível com o perfil | O papel informado não pertence ao perfil selecionado. |
| 400 | Campo inválido | Mensagem do campo (ex.: A senha deve ter entre 8 e 72 caracteres) |
| 400 | Desativar a própria conta | Você não pode desativar a sua própria conta. |
| 400 | Desativar o usuário primário | O usuário primário não pode ser desativado. |
| 400 | Rebaixar o usuário primário | O usuário primário deve manter o perfil Gerência. |
| 401 | Token ausente, inválido ou expirado | Sua sessão expirou ou o token é inválido. Faça login novamente. |
| 403 | Perfil sem permissão | Você não tem permissão para acessar este recurso. |
| 404 | Usuário inexistente | Usuário não encontrado. |
| 409 | E-mail duplicado | Já existe um usuário com este e-mail. |
| 409 | Login duplicado | Já existe um usuário com este nome de usuário. |

Formato do erro: `{ "mensagem": "..." }`
