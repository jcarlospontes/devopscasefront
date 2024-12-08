# Case de Avaliação para Estágio em DevOps

Este repositório tem o objetivo de apresentar o resultado da avaliação para o estágio em DevOps, seguindo as instruções disponíveis em https://github.com/goca-se/devopscasefront
O projeto consiste em uma aplicação web em Node.js que, posteriormente, deverá ser integrada com a aplicação backend disponível no repositório https://github.com/jcarlospontes/devopscaseback

A aplicação no Vercel está disponível no link https://devops-gocase-front-868zz8ry9-joao-carlos-projects-1952645c.vercel.app/

## Objetivo

Criar um Dockerfile para executar a aplicação em um container Docker.

Será necessário configurar uma integração utilizando o GitHub Actions para criar um workflow de CI/CD e, por fim, realizar o deploy na plataforma Vercel.

## Requisitos

Para executar esta avaliação, será necessário:

1. Ter o Docker instalado corretamente: https://docs.docker.com/get-started/get-docker/
   
3. Ter o git instalado corretamente: [https://git-scm.com/downloads](https://git-scm.com/book/pt-br/v2/Come%C3%A7ando-Instalando-o-Git)

## Instruções de uso

Clone o repositório atual utilizando o comando: 
```console
git clone https://github.com/jcarlospontes/devopscasefront.git
```
Acesse a pasta do projeto **devopscasefront**:
```console
cd devopscasefront
```
Crie a imagem da aplicação a partir do Dockerfile:
```console
docker build -t front-app-gocase .
```
Execute a aplicação em segundo plano usando a flag -d e mapeie a porta 80 do host para a porta 80 do container:
bash
Copiar código

```console
docker run -d -p 80:80 front-app-gocase
```

## O que foi feito?

Para a solução, foram realizadas as seguintes etapas:
1. Criação do **Dockerfile** contendo duas imagens:
   
   - **node:22-alpine**: Imagem com um ambiente em node na versão alpine necessário para realizar a instalação das dependências do projeto em node e realizar o build do projeto. A versão 22 foi escolhida pois se trata de uma versão do note recente e faz parte das versões estaveis. A configuração de imagem alpine foi escolhida pois essa nomenclatura se refere a uma imagem leve e segura.
     
   - **nginx:1.22-alpine**: Imagem com um ambiente instalado com nginx. O nginx é uma ferramenta importante que disponibiliza um servidor web que pode ser utilizado para expor várias aplicações frontend. A escolha do nginx foi feita pois conseguimos ter um nível de configuração avançada de endpoints e segurança para a nossa aplicação. A versão 1.22 foi utilizada por ter mais tempo e ser estavel e a nomenclatura alpine foi escolhida pelos mesmos motivos da imagem do node.
  
   Foi utilizado um proxy reverso para o o nginx conseguir mapear o endpoint /api-docs do backend para o mesmo endpoint do frontend.

3. Criação de **dois workflows** executados em um ambiente Ubuntu atualizado:
   
   - **workflowCICD-DEV**: Workflow que simula a implantação em um ambiente de desenvolvimento, onde modificações em branches que não sejam a main ativam a pipeline para realizar etapas de teste, build e deploy.
     
   - **workflowCICD-PRD**: Workflow que simula a implantação em um ambiente de produção, onde modificações na branch main ativam a pipeline de CI/CD.

5. Criar três variáveis de ambiente cadastradas no github secrets:
   
   - VERCEL_ORG_ID: Variável responsável por identificar oid da organização do projeto que foi gerado configurando localmente o projeto através do cadastro do projeto no vercel.
     
   - VERCEL_PROJECT_ID: Variável responsável por identificar o id do projeto que foi gerado configurando localmente o projeto através do cadastro do projeto no vercel.
     
   - VERCEL_TOKEN: Token criado após a criação da conta na Vercel, responsável por integrar a aplicação e o ambiente Vercel.
  
7. Criar dois Jobs no workflow:
   
   - **job-CI**: Se trata de um job que tem a responsabilidade de realizar testes na aplicação e verificar se o build da aplicação ocorre corretamente, para então depois ir para a etapa de CD.
     
     - Primeiro é realizado um checkout no repositório para obter acesso aos arquivos do repositório.
       
     - Depois é utilizado uma versão do node(a mesma utilizada para o build no Dockerfile) para instalação das dependências em node através do **npm install**, execução de testes através do **npm run lint** e a execução do build com **npm run build**
       
   - **job-CD**: Se trata de um job que tem a responsabilidade de realizar o deploy da aplicação na plataforma Vercel.
     - É feito também o checkout para utilizar os arquivos do repositório
     - É instalado o Vercel CLI, que será utilizado para executar comandos do vercel dentro da instancia Ubuntu través do **npm install --global vercel**
     - É feito um pull do projeto para conseguir informações do ambiente que será feito o deploy utilizando **vercel pull --yes --environment=<ambiente>** e o token da conta.
     - É feito o build do projeto através do comando **vercel build** e utilizando o token da conta.
     - Por último é feito o deploy través do **vercel deploy --prebuilt** também utilizando o token da conta.
