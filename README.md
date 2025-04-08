# 📧 Envio de E-mails no Laravel

**Bem-vindo ao guia de implementação de e-mails!** 👋  
*Desenvolvido com ❤️ por Josaid do Nascimento - Desenvolvedor Full Stack*  
*"Construindo pontes digitais, um e-mail de cada vez!"* ✨

Este guia completo vai te ajudar a implementar um sistema de e-mails profissional em aplicações Laravel, com exemplos práticos de registro e notificação de login.

## 🌟 Recursos Implementados
- ✅ E-mail de boas-vindas personalizado
- ✅ Notificação de segurança no login
- ✅ Templates HTML responsivos
- ✅ Configuração para múltiplos provedores SMTP
- ✅ Solução de problemas comuns

## 📋 Pré-requisitos
- Laravel 8.x ou superior
- PHP 7.4 ou superior
- Servidor SMTP configurado (Gmail, Mailtrap, etc.)


## 🚀 Começando

### 1. Configuração Básica
Adicione no seu `.env`:
```env
MAIL_MAILER=smtp
MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USERNAME=seuemail@gmail.com
MAIL_PASSWORD=sua-senha-ou-app-password
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS="seuemail@gmail.com"
MAIL_FROM_NAME="Nome do Seu Sistema"
```

**Dica para Gmail:** Ative "Acesso a app menos seguro" ou crie uma "Senha de app".

### 2. Criando Classes de E-mail
Execute:
```bash
php artisan make:mail UserRegistered
```

Exemplo `UserRegistered.php`:
```php
namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Queue\SerializesModels;
use App\Models\User;

class UserRegistered extends Mailable
{
    use Queueable, SerializesModels;

    public $user;

    public function __construct(User $user)
    {
        $this->user = $user;
    }

    public function build()
    {
        return $this->subject('Bem-vindo ao Sistema')
                   ->view('emails.user_registered')
                   ->with(['user' => $this->user]);
    }
}
```

### 3. Views dos E-mails
`resources/views/emails/user_registered.blade.php`:
```html
<!DOCTYPE html>
<html>
<head>
    <title>Bem-vindo ao Sistema</title>
    <style>
        body { font-family: Arial, sans-serif; line-height: 1.6; }
        .container { max-width: 600px; margin: 0 auto; padding: 20px; }
        .header { background-color: #f8f9fa; padding: 10px; text-align: center; }
        .footer { margin-top: 20px; font-size: 0.8em; color: #6c757d; }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h2>Bem-vindo ao Sistema!</h2>
        </div>
        
        <p>Olá, <strong>{{ $user->name }}</strong>!</p>
        <p>Seu cadastro foi realizado com sucesso.</p>
        <p>Seus dados de acesso:</p>
        <ul>
            <li><strong>E-mail:</strong> {{ $user->email }}</li>
            <li><strong>Data de registro:</strong> {{ $user->created_at->format('d/m/Y H:i') }}</li>
        </ul>
        
        <div class="footer">
            <p>Atenciosamente,<br>Equipe do Sistema</p>
        </div>
    </div>
</body>
</html>
```

### 4. Implementação nos Controllers
**RegisterController:**
```php
use App\Mail\UserRegistered;
use Illuminate\Support\Facades\Mail;

protected function registered(Request $request, $user)
{
    Mail::to($user->email)->send(new UserRegistered($user));
    return redirect()->route('dashboard');
}
```

**LoginController:**
```php
use App\Mail\LoginNotification;
use Illuminate\Support\Facades\Mail;

protected function authenticated(Request $request, $user)
{
    $ip = $request->ip();
    $userAgent = $request->header('User-Agent');
    
    Mail::to($user->email)->send(new LoginNotification($user, $ip, $userAgent));
    return redirect()->intended($this->redirectPath());
}
```

## 🔍 Testando o Envio
**Opção 1 (Log):**
```env
MAIL_MAILER=log
```
*Salva em: storage/logs/laravel.log*

**Opção 2 (Mailtrap):**
```env
MAIL_MAILER=smtp
MAIL_HOST=smtp.mailtrap.io
MAIL_PORT=2525
MAIL_USERNAME=seu-usuario-mailtrap
MAIL_PASSWORD=sua-senha-mailtrap
```

## 🛠 Solução de Problemas
| Problema                  | Solução                                      |
|---------------------------|---------------------------------------------|
| E-mails não enviados      | Verifique .env e logs do Laravel            |
| E-mails no spam           | Configure MAIL_FROM_NAME e MAIL_FROM_ADDRESS|
| Erro de autenticação SMTP | Verifique usuário/senha e servidor          |
| E-mails lentos            | Configure queues (filas)                    |

## 📚 Recursos Adicionais
- [Documentação Oficial](https://laravel.com/docs/mail)
- [Configurar SMTP no Gmail](https://support.google.com/mail/answer/7126229)
- [Mailtrap para testes](https://mailtrap.io/)

## 🤝 Contribuição
Contribuições são bem-vindas! Sinta-se à vontade para abrir issues ou pull requests.

---

**🎯 Importante:** Nunca comite credenciais no código! Sempre use `.env`.
```
