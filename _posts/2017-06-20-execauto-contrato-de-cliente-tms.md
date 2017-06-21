---
layout: post
title: ExecAuto TECA250 - Contrato de Cliente
---
Aqui na empresa temos uma integracao com o SisLog da ESL e fazemos atraves dos XMLs dos CTes.
Tivemos a necessidade de gerarmos os contratos dos clientes, para isso utilizei o _ExecAuto_ do fonte **TECA250**.

```xBase

/*/{Protheus.doc} GeraContrt
	Geracao do Contrato do Cliente
@author PHILIPE.POMPEU
@since 21/03/2017
@version 1.0
@param cCliente, character, (Descricao do parmetro)
@param cLoja, character, (Descricao do parmetro)
@return cContrato, ${return_description}
/*/
Static Function GeraContrt(cCliente,cLoja)
	Local cContrato := ''
	Local aCab := {}
	Local aItensTMS := {}
	Local nI := 0
	Private lMsErroAuto := .F.
	
	AAM->(DbSetOrder(2))	
	if!(AAM->(DbSeek(xFilial("AAM") + cCliente + cLoja)))		
		
		aAdd(aCab,{"AAM_FILIAL"	,xFilial("AAM"),Nil})
		aAdd(aCab,{"AAM_CODCLI"	,SA1->A1_COD,Nil})
		aAdd(aCab,{"AAM_LOJA"	,SA1->A1_LOJA,Nil})
		aAdd(aCab,{"AAM_TPCONT"	,"1",Nil})
		aAdd(aCab,{"AAM_CLASSI"	,"001",Nil})
		aAdd(aCab,{"AAM_ABRANG"	,"2",Nil})
		aAdd(aCab,{"AAM_STATUS"	,"1",Nil})			
		aAdd(aCab,{"AAM_INIVIG"	,IIF(Empty(SA1->A1_PRICOM),CtoD("01/01/2017"),SA1->A1_PRICOM),Nil})
		aAdd(aCab,{"AAM_CPAGPV"	,cCondPag,Nil}) 
		aAdd(aCab,{"AAM_TIPFRE"	,"3",Nil}) 
		aAdd(aCab,{"AAM_REAAUT"	,"2",Nil}) 
		aAdd(aCab,{"AAM_SELSER"	,"1",Nil})
		aAdd(aCab,{"AAM_AGRNFC"	,"1",Nil})
		aAdd(aCab,{"AAM_TAXCTR"	,"1",Nil}) 
		aAdd(aCab,{"AAM_PRCPRD"	,"1",Nil})
		aAdd(aCab,{"AAM_AJUOBR"	,"2",Nil})

		aAdd(aItensTMS,{})		
		
		aAdd(aTail(aItensTMS),{"DUX_FILIAL"	,xFilial("DUX"),Nil})
		aAdd(aTail(aItensTMS),{"DUX_ITEM"	,StrZero(1,2),Nil})		
		aAdd(aTail(aItensTMS),{"DUX_SERVIC"	,cServic,Nil})
		aAdd(aTail(aItensTMS),{"DUX_TABFRE"	,cTabFre,Nil})
		aAdd(aTail(aItensTMS),{"DUX_TIPTAB"	,"01",Nil})
		aAdd(aTail(aItensTMS),{"DUX_FIXVAR"	,"2",Nil})
		aAdd(aTail(aItensTMS),{"DUX_PORTMS"	,"2",Nil})
		aAdd(aTail(aItensTMS),{"DUX_EDITMS"	,"2",Nil})
	
		MSExecAuto({|x,y,z| TECA250("RVM001",x,,,y,z)},aCab,aItensTMS,3)
	
		if(lMsErroAuto)//Se der erro, grava via RecLock			
			cContrato := GetSXENum( "AAM", "AAM_CONTRT" )
			aAdd(aCab,{"AAM_CONTRT",cContrato,Nil})
				
			Begin Transaction
				RecLock("AAM",.T.)				
				aEval(aCab,{|x|AAM->&(x[1]) := x[2]})		
				MsUnlock()
				ConfirmSx8()
				
				for nI:= 1 to Len(aItensTMS)
					aAdd(aItensTMS[nI],{"DUX_NCONTR",cContrato,Nil})					
					RecLock("DUX",.T.)
					aEval(aItensTMS[nI],{|x|DUX->&(x[1]) := x[2]})				
					MsUnlock()
				next nI				
			End Transaction	
		endIf	
	endIf
Return nil
```
Como nao achei nenhum exemplo no **TDN**, decidi compartilhar aqui essa implementacao.