---
layout: post
title: Como eu (quase)automatizei o processo de compila��o do Protheus
---

Aqui na [empresa onde trabalho](https://github.com/rvimola) utilizamos o ERP Microsiga Protheus, e como n�o poderia deixar de ser, temos muitas customiza��es.
As customiza��es do Protheus s�o escritas em [Advpl](http://tdn.totvs.com/x/1YNc), uma linguagem propriet�ria da Totvs padr�o xBase.

### Os problemas
*N�o havia versionamento de fontes, o "controle" era feito de maneira manual e n�o havia um hist�rico dessas altera��es
*Para compilar os fontes Advpl era utilizado o antigo IDE DevStudio, uma IDE bem antiga e que a Totvs j� esta descontinuando
*Todo esse processo era feito de maneira manual, ent�o o desenvolvedor precisava p�r o fonte na pasta do projeto do IDE, depois adicionar ao projeto e enfim compilar num ambiente chamado COMPILADOR
*Ap�s os fontes serem compilados no COMPILADOR, era necess�rio fazer a troca quente, dessa maneira atualizando todos os arquivos appserver.ini(do master e de todos slaves)

###As solu��es encontradas
*O primeiro passo para otimizar nosso processo de deploy foi utilizarmos o Git como sistema de versionamento. Para isso a empresa assinou o GitHub no plano [TEAM](https://github.com/pricing), dessa maneira conseguimos ter reposit�rios privados
*Depois disso, aposentamos o antigo IDE DevStudio e adotamos o novo [TOTVS Developer Studio](http://ds.totvs.com/), que conta com uma [ferramenta de linha de comando](http://tdn.totvs.com/x/ysoGDg)
Com isso foi poss�vel caminharmos em dire��o da automatiza��o do deploy