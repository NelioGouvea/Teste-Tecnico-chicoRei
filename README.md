# Teste-Tecnico-chicoRei
Desenvolvimento de um desafio para teste técnico da vaga de FullStack Developer Jr 

## Tecnologias Utilizadas
- Laravel
- Vue.js
- Bootstrap

## API Consumida
- Link: https://rapidapi.com/okami4kak/api/scrapingant/

## Startando o Projeto
Clone o repositório utilizando o comando 
  git clone https://github.com/NelioGouvea/Teste-Tecnico-chicoRei.git
  
Inicalmente renomeie o arquivo .env.example para .env e altere/preencha os dados do nome do database, usuário e senha
E também lembre-se de inserir a key e o host da api ao final do documento.

##Instalções Necessárias
Digite no seu terminal os seguintes comandos para instalar as dependências
  composer install
  npm install
  npm run dev
  
Agora para rodar o projeto os comandos abaixo:
  php artisan key:generate
  php artisan migrate
  
Finalmente para iniciar o servidor local e ver o resultado rode o seguinte comando
  php artisan serve
