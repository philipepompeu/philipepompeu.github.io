---
layout: post
title: Como eu (quase)automatizei o processo de compilação do Protheus
---

Aqui na [empresa onde trabalho](https://github.com/rvimola) utilizamos o ERP Microsiga Protheus, e como não poderia deixar de ser, temos muitas customizações.
As customizações do Protheus são escritas em [Advpl](http://tdn.totvs.com/x/1YNc), uma linguagem proprietária da Totvs padrão xBase.

### Os problemas
*Não havia versionamento de fontes, o "controle" era feito de maneira manual e não havia um histórico dessas alterações
*Para compilar os fontes Advpl era utilizado o antigo IDE DevStudio, uma IDE bem antiga e que a Totvs já esta descontinuando
*Todo esse processo era feito de maneira manual, então o desenvolvedor precisava pôr o fonte na pasta do projeto do IDE, depois adicionar ao projeto e enfim compilar num ambiente chamado COMPILADOR
*Após os fontes serem compilados no COMPILADOR, era necessário fazer a troca quente, dessa maneira atualizando todos os arquivos appserver.ini(do master e de todos slaves)

###As soluções encontradas
*O primeiro passo para otimizar nosso processo de deploy foi utilizarmos o Git como sistema de versionamento. Para isso a empresa assinou o GitHub no plano [TEAM](https://github.com/pricing), dessa maneira conseguimos ter repositórios privados
*Depois disso, aposentamos o antigo IDE DevStudio e adotamos o novo [TOTVS Developer Studio](http://ds.totvs.com/), que conta com uma [ferramenta de linha de comando](http://tdn.totvs.com/x/ysoGDg)
Com isso foi possível caminharmos em direção da automatização do deploy