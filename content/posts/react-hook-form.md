+++
title = "Formulários com React Hook Form + Zod"
date = "2024-11-07T11:58:13-03:00"
#dateFormat = "YYYY-MM-DD" # This value can be configured for per-post date formatting
author = "Lucas Silva"
authorTwitter = "" #do not include @
cover = ""
tags = ["react", "web"]
keywords = ["", ""]
description = "Facilite a criação de formulários em react"
showFullContent = false
readingTime = false
hideComments = false
+++

## Introdução
Quando comecei a programar, precisava escrever bastante código em JavaScript puro para coletar dados de um formulário. De lá para cá, o desenvolvimento web evoluiu tanto que hoje temos bibliotecas que abstraem grande parte desse trabalho, facilitando a gestão dos dados dos formulários.

Neste artigo, vou mostrar como utilizar o [React Hook Form](https://www.react-hook-form.com/) para trabalhar com dados de formulários e [zod](https://zod.dev/) para validar esses dados de forma eficiente e organizada.

## Tô com pressa, me dê o código completo
Tá na mão:
``` tsx
import { Label } from '@/components/ui/label';
import { Input } from '@/components/ui/input';
import { Button } from '@/components/ui/button';
import { Gamepad, Gamepad2 } from 'lucide-react';
import { useForm } from 'react-hook-form';
import { z } from 'zod';
import { zodResolver } from '@hookform/resolvers/zod';
import { toast, Toaster } from 'sonner';

const signUpForm = z
  .object({
    name: z
      .string()
      .min(2, { message: 'Nome deve ter ao menos 2 caracteres' })
      .max(50, { message: 'Nome deve ter no máximo 50 caracteres' }),
    email: z.string().email().max(100, { message: 'E-mail deve ter no máximo 100 caracteres' }),
    password: z.string().max(100, { message: 'Senha deve ter no máximo 100 caracteres' }),
    confirm: z.string(),
  })
  .refine((data) => data.password === data.confirm, {
    message: 'Senhas não conferem!',
    path: ['confirm'],
  });

type SignUpForm = z.infer<typeof signUpForm>;

export function App() {
  const {
    handleSubmit,
    register,
    reset,
    formState: { isSubmitting, errors },
  } = useForm<SignUpForm>({
    resolver: zodResolver(signUpForm),
  });

  async function handleSignup(data: SignUpForm) {
    console.log(data);
    await new Promise((resolve) => {
      setTimeout(resolve, 2000);
    });
    reset();
    toast.success('Conta criada com sucesso!');
  }

  return (
    <>
      <Toaster richColors />
      <div className="min-h-screen lg:grid lg:grid-cols-2 antialiased gap-8">
        <div className="hidden lg:flex h-full justify-center border-r border-foreground/5 bg-foreground text-muted-foreground items-center gap-3 text-lg">
          <Gamepad2 />
          <span className="font-semibold">gamers.shop</span>
        </div>
        <div className="flex flex-col items-center justify-center gap-6 min-h-screen">
          <div className="px-10 w-96 h-full flex flex-col justify-center items-center lg:w-[500px]">
            <h1 className="flex items-center gap-2 mb-4 text-2xl font-semibold tracking-tight">
              <Gamepad />
              Crie sua conta
              <Gamepad />
            </h1>
            <form onSubmit={handleSubmit(handleSignup)} className="space-y-4 w-full ">
              <div className="space-y-2">
                <Label htmlFor="name">Nome</Label>
                <Input id="name" type="text" {...register('name')} />
                {errors.name && <span className="text-sm text-red-500">{errors.name.message}</span>}
                {}
              </div>
              <div className="space-y-2">
                <Label htmlFor="email">Email</Label>
                <Input id="email" type="email" {...register('email')} />
                {errors.email && <span className="text-sm text-red-500">{errors.email.message}</span>}
              </div>
              <div className="space-y-2">
                <Label htmlFor="password">Senha</Label>
                <Input id="password" type="password" {...register('password')} />
                {errors.password && <span className="text-sm text-red-500">{errors.password.message}</span>}
              </div>
              <div className="space-y-2">
                <Label htmlFor="confirm">Confirme a senha</Label>
                <Input id="confirm" type="password" {...register('confirm')} />
                {errors.confirm && <span className="text-sm text-red-500">{errors.confirm.message}</span>}
              </div>

              <Button className="w-full" type="submit" disabled={isSubmitting}>
                Criar conta
              </Button>
            </form>
          </div>
        </div>
      </div>
    </>
  );
}

```
## Tutorial

### Estruturando o Formulário
Primeiro, criei um formulário com quatro campos: nome, e-mail, senha e confirmação de senha. Para facilitar o desenvolvimento da interface, utilizei `shadcn`, `tailwind` e `lucide-react`. O uso de classes CSS pode parecer um pouco detalhado, mas elas ajudam a manter um design consistente.
```tsx
<form className="space-y-4 w-full ">
  <div className="space-y-2">
    <Label htmlFor="name">Nome</Label>
    <Input id="name" type="text" placeholder="Digite o seu nome"/>
  </div>
  <div className="space-y-2">
    <Label htmlFor="email">Email</Label>
    <Input id="email" type="email" placeholder="Digite o seu e-mail"/>
  </div>
  <div className="space-y-2">
    <Label htmlFor="password">Senha</Label>
    <Input id="password" type="password" placeholder="Digite uma senha"/>
  </div>
  <div className="space-y-2">
    <Label htmlFor="confirm">Confirme a senha</Label>
    <Input id="confirm" type="password" placeholder="Digite a senha novamente"/>
  </div>

  <Button className="w-full" type="submit">
    Criar conta
  </Button>
</form>
```

Criei 4 campos nesse form: nome, e-mail, password e confirm. Preciso validá-los de alguma forma. Como esse exemplo server para explicar o uso de zod, evitei utilizar as propriedades nativas do HTML (required, maxlength etc).
### Validação de Dados com Zod
Para garantir que os dados inseridos nos campos estão corretos, criei um schema de validação com o `zod`. O schema define as restrições para cada campo e personaliza as mensagens de erro:
```tsx
import { z } from 'zod';

const signUpFormSchema = z
  .object({
    name: z
      .string()
      .min(2, { message: 'Nome deve ter ao menos 2 caracteres' })
      .max(50, { message: 'Nome deve ter no máximo 50 caracteres' }),
    email: z.string().email().max(100, { message: 'E-mail deve ter no máximo 100 caracteres' }),
    password: z.string().max(100, { message: 'Senha deve ter no máximo 100 caracteres' }),
    confirm: z.string(),
  })
  .refine((data) => data.password === data.confirm, {
    message: 'Senhas não conferem!',
    path: ['confirm'],
  });

```

Esse schema define os tipos e as validações necessárias para cada campo. O método `.refine()` foi utilizado para garantir que as senhas digitadas nos campos "password" e "confirm" são iguais.
```tsx
.refine((data) => data.password === data.confirm, {
  message: 'Senhas não conferem!',
  path: ['confirm'],
});
```

### Integração com React Hook Form e Zod Resolver
O `React Hook Form` é uma biblioteca de formulários que melhora o desempenho ao reduzir re-renderizações desnecessárias e simplificar a manipulação de dados. Usei o `useForm()` para configurar o formulário, passando o schema de validação por meio do `zodResolver`:
```tsx
import { zodResolver } from '@hookform/resolvers/zod';
import { useForm } from 'react-hook-form';

type SignUpForm = z.infer<typeof signUpFormSchema>;

const {
    handleSubmit,
    register,
    reset,
    formState: { isSubmitting, errors },
  } = useForm<SignUpForm>({
    resolver: zodResolver(signUpFormSchema),
  });
```
As funções e variáveis que utilizei de `useForm()` são:
- `handleSubmit`: lida com o envio do formulário.
- `register`: vincula campos do formulário ao gerenciamento de dados do React Hook Form.
- `reset`: redefine o formulário.
- `isSubmitting`: indica se o formulário está sendo enviado.
- `errors`: armazena erros de validação de cada campo.
### Função de Envio do Formulário
Para simular o envio do formulário, criei a função `handleSignup`, que adiciona um tempo de espera para visualizar o estado `isSubmitting` e exibe uma mensagem de sucesso com o `toast`:
```tsx
async function handleSignup(data: SignUpForm) {
  console.log(data);
  await new Promise((resolve) => {
    setTimeout(resolve, 2000);
  });
  reset();
  toast.success('Conta criada com sucesso!');
}
```

Depois adicionei a função ao form:
```tsx
<form onSubmit={handleSubmit(handleSignup)} className="space-y-4 w-full">
```

E por fim, utilizei o register para denominar qual campo pertence a qual propriedade, e mostrei os erros (caso eles existam):
```jsx
<div className="space-y-2">
  <Label htmlFor="name">Nome</Label>
  <Input id="name" type="text" {...register('name')} placeholder="Digite o seu nome" />
  {errors.name && <span className="text-sm text-red-500">{errors.name.message}</span>}
</div>
```

O resultado ficou assim:

![Tela de criação de conta feita neste tutorial](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/oipm2sov0dn33pnulzva.png)

## Considerações Finais
Nesse texto, mostrei uma forma simples de integrar `React Hook Form` e `zod` para validação de formulários não controlados. A biblioteca também funciona com componentes controlados, então consulte a [documentação](https://www.react-hook-form.com/get-started/) para explorar mais opções.
