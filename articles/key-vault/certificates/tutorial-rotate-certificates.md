---
title: Självstudie – uppdatera certifikatets frekvens för automatisk rotation i Key Vault | Microsoft Docs
description: Självstudie som visar hur du uppdaterar ett certifikats frekvens för automatisk rotation i Azure Key Vault med hjälp av Azure Portal
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 2e6c250a0bcb9d73e7c572dfe8138c31269993e8
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106806"
---
# <a name="tutorial-configuring-certificates-auto-rotation-in-key-vault"></a>Självstudie: Konfigurera certifikatets Auto-rotation i Key Vault

Med Azure Key Vault kan du enkelt etablera, hantera och distribuera digitala certifikat. De kan vara offentliga och privata SSL/TLS-certifikat signerade av certifikat utfärdare eller självsignerade certifikat. Key Vault kan också begära och förnya certifikat via partnerskap med certifikat utfärdare, vilket ger en robust lösning för hantering av certifikat livs cykeln. I den här självstudien kommer du att uppdatera certifikatets attribut – giltighets period, frekvens för automatisk rotation, CA. Mer information om nyckelvalv finns i [översikten](../general/overview.md).

Självstudien visar hur du:

> [!div class="checklist"]
> * Hantera ett certifikat med hjälp av Azure Portal
> * Lägg till provider-konto för certifikat utfärdare
> * Uppdatera certifikatets giltighets period
> * Uppdatera certifikatets frekvens för automatisk rotation
> * Uppdatera certifikatets attribut med Azure PowerShell


Läs [Key Vault grundläggande koncept](../general/basic-concepts.md)innan du börjar. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-vault"></a>Skapa ett valv

Skapa eller välj din befintliga Key Vault för att utföra åtgärder. [(Steg för att skapa ett nyckel valv).](../quick-create-portal.md) I exemplet är valv namnet **exempel – valv**. 

![Utdata när nyckelvalvet har skapats](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Skapa ett certifikat i Key Vault

Skapa eller importera ett certifikat i valvet. [(Steg för att skapa certifikat i Key Vault).](../quick-create-portal.md) I det här fallet arbetar vi med certifikat som heter **ExampleCertificate**.

> [!NOTE]
> I Azure Key Vault kan ett certifikats livs cykel attribut uppdateras både vid tidpunkten för certifikatets skapande och efter att det har skapats. 
## <a name="updating-certificates-life-cycle-attributes"></a>Uppdaterar certifikatets livs cykel attribut

Ett certifikat som skapats i Key Vault kan vara 
- ett självsignerat certifikat
- ett certifikat som har skapats med en certifikat utfärdare (CA) som är partner med Key Vault
- ett certifikat med en certifikat utfärdare som inte är partner med Key Vault

Följande certifikat utfärdare är för närvarande partner leverantörer med Key Vault:
- DigiCert-Key Vault erbjuder OV TLS/SSL-certifikat med DigiCert.
- GlobalSign-Key Vault erbjuder OV TLS/SSL-certifikat med GlobalSign.

Azure Key Vault automatiskt roterar certifikat via partnerskap med certifikat utfärdare. Via det etablerade partnerskapet Key Vault begär och förnya certifikat automatiskt. **Det är därför inte möjligt att använda automatisk rotation för certifikat som skapats med ca: er som inte är partner med Key Vault.** 

> [!NOTE]
> En konto administratör för en CA-Provider skapar autentiseringsuppgifter som ska användas av Key Vault för att skapa, förnya och använda TLS/SSL-certifikat via Key Vault.
![Certifikatutfärdare](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
> 


### <a name="updating-certificates-life-cycle-attributes-at-the-time-of-certificate-creation"></a>Uppdaterar certifikatets livs cykel attribut när certifikatet skapas

1. Välj **certifikat**på sidan Key Vault egenskaper.
2. Klicka på **Generera/importera**.
3. På skärmen **skapa ett certifikat** uppdaterar du följande värden:
    

    - **Giltighets period**: Ange värdet (i månader). Att skapa korta livs längds certifikat är en rekommenderad säkerhets rutin. Som standard är giltighets värde för ett nyligen skapat certifikat 12 månader.
    - **Livs längds åtgärds typ**: Välj certifikatets automatiska förnyelse-och aviserings åtgärd. Enligt valet uppdaterar du procentens livs längd eller antal dagar innan det går ut. Som standard anges ett certifikats automatiska förnyelse till 80% av dess livs längd.<br> Välj alternativet på den nedrullningsbara menyn:

    |  Förnya automatiskt vid en specifik tidpunkt| Skicka e-post till alla kontakter vid en specifik tidpunkt |
    |-----------|------|
    |Om du väljer det här alternativet AKTIVe ras autorotation | Om du väljer det här alternativet roteras den inte automatiskt, utan aviseringar skickas|
        


4. Klicka på **Skapa**.

![Certifikatets livs cykel](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="updating-life-cycle-attributes-of-stored-certificate"></a>Uppdaterar livs cykel attribut för lagrat certifikat

1. Välj Key Vault.
2. Välj **certifikat**på sidan Key Vault egenskaper.
3. Välj det certifikat som du vill uppdatera. I det här fallet kommer vi att arbeta med certifikatet som heter **ExampleCertificate**.
4. Välj **utgivnings princip** på den översta meny raden.

![Certifikat egenskaper](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)
5. På skärmen **utgivnings princip** uppdaterar du följande värden:
- **Giltighets period**: uppdatera värdet (i månader)
- **Livs längds åtgärds typ**: Välj certifikatets automatiska förnyelse-och aviserings åtgärd. Efter valet uppdaterar du procents livs längd eller antal dagar innan det går ut. 

![Certifikat egenskaper](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)
6. Klicka på **Spara**.

> [!IMPORTANT]
> Om du ändrar livs längds åtgärds typen för ett certifikat registreras ändringarna för de befintliga certifikaten omedelbart.


### <a name="updating-certificates-attributes-using-powershell"></a>Uppdaterar certifikatets attribut med PowerShell

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> Ändra förnyelse principen för en lista över certifikat, indatafil. csv som innehåller VaultName, CertName <br/>
>  vault1,Cert1 <br/>
>  vault2, Cert2
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
Läs mer om parametrarna [här](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-set-attributes)

## <a name="clean-up-resources"></a>Rensa resurser

Andra snabbstarter och självstudier om Key Vault bygger på den här snabbstarten. Om du planerar att fortsätta med efterföljande snabbstarter och självstudier kan du lämna kvar de här resurserna.
När du inte behöver resursgruppen längre så tar du bort den. Då tas även nyckelvalvet och relaterade resurser bort. Så här tar du bort resursgruppen via portalen:

1. Skriv namnet på resursgruppen i rutan Sök längst upp i portalen. När du ser resursgruppen du använde i den här snabbstarten bland sökresultaten väljer du den.
2. Välj **Ta bort resursgrupp**.
3. I rutan **SKRIV RESURSGRUPPSNAMNET:** skriver du namnet på resursgruppen och väljer **Ta bort**.


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du uppdaterat ett certifikats livs cykel. Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

Läs mer om att [Hantera certifikat skapande i Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-scenarios)
- Granska [Key Vault översikt](../general/overview.md)