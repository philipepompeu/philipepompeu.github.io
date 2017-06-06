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
### Etapas
Primeiro criamos uma [organiza&ccedil;&atilde;o](https://github.com/rvimola) no GitHub para empresa e criamos o reposit&oacute;rio que armazenar&aacute; os fontes customizados
![Criando um repositorio](/img/01/novo_repositorio.JPG)
Todo o processo de utiliza&ccedil;ao do Git pode ser lido [aqui](https://git-scm.com/book/pt-br/v1/Primeiros-passos). Nao vou entrar em muitos detalhes sobre sua utiliza&ccedil;ao porque a internet esta cheia de tutoriais.

N&oacute;s instalamos o [GitHub Desktop](https://desktop.github.com/) no servidor porque &eacute; mais f&aacute;cil utilizando uma ferramenta visual para clonar o reposit&oacute;rio e configurar o ambiente no inicio.

Apos clonar o remote &eacute; faca o pull dos fontes(va ate o reposit&oacute;rio via cmd e de um git pull). 
Voce percebera que sera necessario informar o usuario e a senha do seu usuario do GitHub, para armazenar as suas credenciais e nao precisar reinformar todas as vezes que for dar um git pull, use o [Git Credential Store](https://www.kernel.org/pub/software/scm/git/docs/git-credential-store.html)
2. Depois instalamos o [TDS](http://ds.totvs.com/) no servidor, no site oficial do TDS h&aacute; informacoes sobre a sua instalacao e configuracao.
Al&eacute;m de instalar o TDS &eacute; preciso instalar um adicional chamado [TDSCli](http://tdn.totvs.com/x/ysoGDg), que &eacute; a ferramenta de linha de comando do TDS.
3. Na primeira vez que voce logar no TDS ele solicitara que escolha a localizacao da sua workspace, escolha a pasta onde se encontra o seu repositorio local do Git(que voce deve ter clonado a partir do seu remote).
4. Crie um projeto no TDS com o mesmo nome do seu repositorio. O TDS vai criar duas pastas por padrao dentro da pasta principal, costumo remove-las.