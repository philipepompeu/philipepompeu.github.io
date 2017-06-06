---
layout: post
title: Como eu (quase)automatizei o processo de compila&ccedil;&atilde;o do Protheus
---
Aqui na [empresa onde trabalho](https://github.com/rvimola) utilizamos o ERP Microsiga Protheus, e como n&atilde;o poderia deixar de ser, temos muitas customiza&ccedil;&otilde;es.
As customizacoes do Protheus s&atilde;o escritas em [Advpl](http://tdn.totvs.com/x/1YNc), uma linguagem propriet&aacute;ria da Totvs padr&atilde;o xBase.
### Os problemas
* N&atilde;o havia versionamento de fontes, o "controle" era feito de maneira manual e n&atilde;o havia um hist&oacute;rico dessas altera&ccedil;&otilde;es
* Para compilar os fontes Advpl era utilizado o antigo IDE DevStudio, uma IDE bem antiga e que a Totvs j&aacute; esta descontinuando
* Todo esse processo era feito de maneira manual, ent&atilde;o o desenvolvedor precisava p&ocirc;r o fonte na pasta do projeto do IDE, depois adicionar ao projeto e enfim compilar num ambiente chamado COMPILADOR
* Ap&oacute;s os fontes serem compilados no COMPILADOR, era necess&aacuterio fazer a troca quente, dessa maneira atualizando todos os arquivos appserver.ini(do master e de todos slaves)
### As solu&ccedil;&otilde;es encontradas
* O primeiro passo para otimizar nosso processo de deploy foi utilizarmos o Git como sistema de versionamento. Para isso a empresa assinou o GitHub no plano [TEAM](https://github.com/pricing), dessa maneira conseguimos ter reposit&oacute;rios privados
* Depois disso, aposentamos o antigo IDE DevStudio e adotamos o novo [TOTVS Developer Studio](http://ds.totvs.com/), que conta com uma [ferramenta de linha de comando](http://tdn.totvs.com/x/ysoGDg)
Com isso foi possivel caminharmos em dire&ccedil;&atilde;o da automatiza&ccedil;&atilde;o do deploy