---
layout: post
title: Gerando usuarios para o Portal do Fornecedor
---
Aqui na empresa decidimos implantar o Portal do Fornecedor do Protheus para que nossos fornecedores conseguissem atualizar suas informacoes e consultar suas cotacoes, pedidos etc.

Tivemos a necessidade de gerarmos de maneira automatica o usuario do Portal e enviar suas credenciais por e-mail. Abaixo um exemplo de como fizemos:

```xBase
//Retorna ou Gera o usuario do Portal para o <cForn + cLoja>
Static Function GenUsrWb(cForn, cLoja)
	Local aResult	:= {}
	Local oModel	:= Nil
	Local oMaster := Nil
	Local oMdlAI5 := Nil
	Local oMdlAI6 := Nil
	Local cDefaultPsw := "abc#123"
	Local cFiltro := ""
	Local bFiltro := {||}
	Local nLinha := 0
	Local nTamWbSrv := TamSX3("AI6_WEBSRV")[1]
	Local cCodUsu := ''
	
	aResult := GetUsrFor(cForn, cLoja)
	if!(Len(aResult) == 4)
		SA2->(DbSetOrder(1))
		if(SA2->(DbSeek(xFilial("SA2") + cForn + cLoja)))
			oModel := FWLoadModel("FATA220")
			oModel:SetOperation(3)
			oModel:Activate()
			oMaster		:= oModel:GetModel("AI3MASTER")
			oMdlAI5	:= oModel:GetModel("AI5DETAIL")
			oMdlAI6	:= oModel:GetModel("AI6DETAIL")
			
			oMaster:LoadValue("AI3_LOGIN"	,SA2->A2_CGC)
			oMaster:LoadValue("AI3_EMAIL"	,PadR(SA2->A2_EMAIL,TamSX3("AI3_EMAIL")[1]))
			oMaster:LoadValue("AI3_ADMIN"	,"2")
			oMaster:LoadValue("AI3_PSW"		,cDefaultPsw)
			oMaster:LoadValue("AI3_NOME"	,PadR(SA2->A2_NREDUZ,TamSX3("AI3_NOME")[1]))
							
			oMdlAI5:LoadValue("AI5_CODFOR",SA2->A2_COD)
			oMdlAI5:LoadValue("AI5_LOJFOR",SA2->A2_LOJA)
			
			AI7->(dbSetOrder(1))
			
			cFiltro := "AI7_FILIAL = '"+ xFilial("AI7") +"'"
			bFiltro := &("{||"+ cFiltro  +"}")			
			AI7->(DbSetFilter(bFiltro,cFiltro))
			AI7->(DbGoTop())
			
			cCodUsu := oMaster:GetValue("AI3_CODUSU")
			nLinha := 1	
			While AI7->(!Eof())
				if(nLinha > 0)
					oMdlAI6:GoLine(nLinha)
					oMdlAI6:LoadValue("AI6_FILIAL",xFilial("AI6"))
					oMdlAI6:LoadValue("AI6_WEBSRV",PadR(AI7->AI7_WEBSRV,nTamWbSrv))
					oMdlAI6:LoadValue("AI6_NOMEWS",AllTrim(AI7->AI7_DESCRI))				
				endIf
				nLinha := oMdlAI6:AddLine()
				AI7->(dbSkip())
			EndDo
			AI7->(DBClearFilter())			
			oMdlAI6:GoLine(1)
			
			FWFormCommit(oModel)		
			oModel:Deactivate()
			oModel:Destroy()
			oModel:= nil
			
			aAdd(aResult,AI3->AI3_CODUSU)
			aAdd(aResult,AI3->AI3_LOGIN)
			aAdd(aResult,AI3->AI3_PSW)
			aAdd(aResult,.T.) //Eh novo			
		endIf
	endIf	
Return aResult

//Confere a existencia do usuario do Fornecedor
Static Function GetUsrFor(cForn, cLoja)
	Local aResult := {}	
	Local cMyAlias := GetNextAlias()
	Local cQuery := ''
	
	cForn := PadR(cForn,TamSX3('AI5_CODFOR')[1])
	cLoja := PadR(cLoja,TamSX3('AI5_LOJFOR')[1])
	
	cQuery := "SELECT AI5_CODUSU,AI3_LOGIN, AI3_PSW FROM " + RetSqlName('AI5') + " AI5 "
	cQuery += " INNER JOIN "+ RetSqlName('AI3') +" AI3 ON(AI3_FILIAL = '"+ xFilial("AI3") +"'"
	cQuery += " AND AI3.D_E_L_E_T_ = ' ' AND AI3_CODUSU =  AI5_CODUSU)"
	cQuery += " WHERE AI5.D_E_L_E_T_ = ' ' AND AI5_FILIAL = '"+ xFilial("AI5") + "'" 
	cQuery += " AND AI5_CODFOR = '"+ cForn +"' AND AI5_LOJFOR = '"+ cLoja +"'"
	cQuery	:= ChangeQuery(cQuery)				
	DbUseArea(.T., "TOPCONN", TCGenQry(,,cQuery), cMyAlias, .F., .T.)
	
	if((cMyAlias)->(! Eof()))				
		aAdd(aResult,(cMyAlias)->AI5_CODUSU)
		aAdd(aResult,(cMyAlias)->AI3_LOGIN)
		aAdd(aResult,(cMyAlias)->AI3_PSW)
		aAdd(aResult,.F.) //Diz se o registro eh novo ou ja existia(que eh o caso nessa situacao)
	EndIf
	(cMyAlias)->(dbCloseArea())	
Return aResult
```
Utilizamos o "ExecAuto" do FATA220, com as credencias geradas nos enviamos via workflow pro fornecedor.