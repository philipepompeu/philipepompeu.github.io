---
layout: post
title: ExecAuto TMSA410 - Regras de Tributacao
---
Aqui na empresa temos uma integracao com o SisLog da ESL e fazemos atraves dos XMLs dos CTes.
Como nao existe nenhum exemplo no **TDN**, decidi compartilhar essa implementacao.
Tivemos a necessidade de gerarmos de maneira automatica a regra de tributacao, para isso utilizei o _ExecAuto_(na verdade trata-se de um fonte **MVC**) do fonte **TMSA410**.
```advpl
/*/{Protheus.doc} GenRgrTrib
	Geracao de Regras de Tributao
@author PHILIPE.POMPEU
@since 22/03/2017
@param cUFOrig, caractere, Uf de origem
@param cUFDest, caractere, UF de Destino
@param cUFPag, caractere, UF do pagador
@param cTes, caractere, Tipo de Entrada/Saida
/*/Static Function GenRgrTrib(cUFOrig, cUFDest, cUFPag,cTes)
	Local aAreas := {DUF->(GetArea()),GetArea()}
	Local cChave := ''
	Local aRegistro := {}
	Local oModel := Nil
	Local oSubModel := Nil
	Local nI := 0
	Local cProxId := '01'
	
	DUF->(DbSetOrder(1))
	cChave := xFilial("DUF") + "01" + "3"
	if(DUF->(dbSeek(cChave))) //DUF_FILIAL + DUF_REGTRI + DUF_TIPFRE
		
		oModel := FWLoadModel("TMSA410")
		oModel:SetOperation(4)
		oModel:Activate()
		
		oSubModel := oModel:GetModel("MdGridIDUG")
		
		aAdd(aRegistro,{"DUG_ESTORI",cUFOrig})
		aAdd(aRegistro,{"DUG_ESTDES",cUFDest})
				
		oSubModel:SetLine(1)		
		if(!oSubModel:SeekLine(aRegistro))
			if((oSubModel:Length() > 1) .Or. ((oSubModel:Length() == 1) .And. !oSubModel:IsInserted()))
				oSubModel:GoLine(oSubModel:Length()) //Vai pra ultima linha	
				cProxId := oSubModel:GetValue("DUG_ITEM")
				cProxId := Soma1(cProxId)
				oSubModel:AddLine()//Adiciona uma nova linha caso nao encontre!
			endIf
			aAdd(aRegistro,{"DUG_ITEM",cProxId})
		endIf
		
		aAdd(aRegistro,{"DUG_TES"	,cTes})
		aAdd(aRegistro,{"DUG_CONSIG","3"})
		
		aEval(aRegistro,{|x|oSubModel:LoadValue(x[1],x[2])})
		
		FWFormCommit(oModel)
		
		oModel:Deactivate()
		oModel:Destroy()
		oModel:= nil
		aSize(aRegistro,0)
		aRegistro := nil
	endIf	
	
	aEval(aAreas,{|x|RestArea(x)})
Return nil
```
Como nao achei nenhum exemplo no TDN, decidi compartilhar aqui.