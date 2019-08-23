---
title: Så här hanterar du OPC Vault Certificate Management Service – Azure | Microsoft Docs
description: Hantera OPC-valvets rot certifikat och användar behörigheter.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6e53914b16bc126cdab7300d918dbb7b6b868728
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2019
ms.locfileid: "69973810"
---
# <a name="how-to-manage-the-opc-ua-certificate-service"></a>Hantera OPC UA Certificate service

I den här artikeln beskrivs administrativa uppgifter för OPC UA Certificate Management-tjänsten i Azure, hur du förnyar certifikat utfärdares certifikat för certifikat utfärdare, hur du förnyar listan över återkallade certifikat (CRL) och hur du tilldelar och återkallar användar åtkomst.

## <a name="create-or-renew-the-root-ca-certificate"></a>Skapa eller förnya rot certifikat utfärdarens certifikat

För att skapa rot certifikat utfärdarens certifikat är ett obligatoriskt steg efter distributionen. Utan ett giltigt CA-certifikat för certifikat utfärdare kan inga program certifikat signeras och utfärdas.<br>Se kapitlet om [certifikat livs längder](howto-opc-vault-secure-ca.md#certificates) för att hantera dina certifikat med rimliga, säkra livs längder. Ett CA-certifikat för utfärdande ska förnyas efter hälften av livs längden, men inte senare än innan den konfigurerade livs längden för ett nytt signerat program certifikat skulle överskrida livs längden för utfärdarens certifikat.<br>
> [!IMPORTANT]
> Administratörs rollen krävs för att skapa eller förnya utfärdarens CA-certifikat.

1. Öppna certifikat tjänsten på `https://myResourceGroup-app.azurewebsites.net` och logga in.
2. Gå till `Certificate Groups` sidan.
3. En `Default` certifikat grupp visas. Klicka på `Edit`.
4. I `Edit Certificate Group Details` kan du ändra ämnes namn och livs längd för certifikat utfärdaren och program certifikaten.<br>Ämnet och livs längden bör endast anges en gång innan det första CA-certifikatet utfärdas. Livs längds ändringar under åtgärder kan resultera i inkonsekventa livs längder för utfärdade certifikat och CRL: er.
5. Ange ett giltigt ämne, till exempel `CN=My CA Root, O=MyCompany, OU=MyDepartment`.<br>
   > [!IMPORTANT]
   > Att ändra ämnet kräver att förnya utfärdarens certifikat, eller att tjänsten inte kan signera program certifikat. Ämnet för konfigurationen är Sanity kontrol leras mot ämnet för det aktiva utfärdarcertifikatet. Om ämnena inte matchar, nekas certifikat signering.
6. Klicka på knappen `Save` .
7. Om du trycker på ett "förbud"-fel nu har dina användarautentiseringsuppgifter inte administratörs behörighet för att ändra eller skapa ett nytt rot certifikat. Som standard har den användare som har distribuerat tjänsten administratörs-och signerings roller med tjänsten, andra användare måste läggas till i rollerna "god kännare", "skribent" eller "administratör", enligt vad som är lämpligt i AzureAD program registrering.
8. Klicka på knappen `Details` . Den uppdaterade informationen ska visas. `View Certificate Group Details`
9. Klicka på `Renew CA Certificate` knappen för att utfärda det första utfärdarens CA-certifikat eller förnya utfärdarcertifikatet. Tryck `Ok` för att fortsätta.
10. Efter några sekunder `Certificate Details` visas. Tryck `Issuer` på `Crl` eller för att ladda ned det senaste ca-certifikatet och CRL för distribution till dina OPC UA-program.
11. Nu är OPC UA Certificate Management-tjänsten redo att utfärda certifikat för OPC UA-program.

## <a name="renew-the-crl"></a>Förnya CRL

Förnyelse av listan över återkallade certifikat (CRL) är en uppdatering som bör distribueras till program med jämna mellanrum. OPC UA-enheter, som har stöd för X509-tillägget för CRL-distributions platsen, kan uppdatera CRL: en direkt från mikrotjänstens slut punkt. Andra OPC UA-enheter kan kräva manuella uppdateringar eller det bästa fallet kan uppdateras med GDS-serverns push-tillägg (*) för att uppdatera listan över betrodda certifikat med certifikaten och listorna över återkallade certifikat.

I följande arbets flöde återkallas alla certifikat begär anden i de borttagna tillstånden i listor över återkallade certifikat, som motsvarar utfärdare av certifikat UTFÄRDAre som de har utfärdat för. Versions numret för CRL: en ökas med 1. <br>
> [!NOTE]
> Alla utfärdade CRL: er är giltiga tills certifikat UTFÄRDARens giltighets tid, eftersom OPC UA-specifikationen inte kräver en obligatorisk, deterministisk distributions modell för CRL.

> [!IMPORTANT]
> Administratörs rollen krävs för att förnya utfärdarens CRL.

1. Öppna certifikat tjänsten på `https://myResourceGroup.azurewebsites.net` och logga in.
2. Gå till `Certificate Groups` sidan.
3. Klicka på knappen `Details` . Det aktuella certifikatet och CRL-informationen skavisas.`View Certificate Group Details`
4. Klicka på `Update CRL Revocation List(CRL)` knappen för att utfärda en uppdaterad lista över återkallade certifikat för alla aktiva utfärdarcertifikat i OPC Vault-lagringen.
5. Efter några sekunder `Certificate Details` visas. Tryck `Issuer` på `Crl` eller för att ladda ned det senaste ca-certifikatet och CRL för distribution till dina OPC UA-program.

## <a name="manage-user-roles"></a>Hantera användar roller

Användar roller för mikrotjänsten i OPC Vault hanteras i Azure Active Directory Enterprise-programmet.

En detaljerad beskrivning av roll definitionerna hittar du i avsnittet [roller](howto-opc-vault-secure-ca.md#roles) .

Som standard kan en autentiserad användare i klienten logga in i tjänsten som en läsare. Högre privilegierade roller kräver manuell hantering i Azure Portal eller med hjälp av PowerShell.

### <a name="add-user"></a>Lägg till användare

1. Öppna Azure Portal på `portal.azure.com`.
2. Navigera till `Azure Active Directory`/`Enterprise applications`.
3. Välj registreringen av mikrotjänsten OPC Vault som standard `resourceGroupName-service`.
4. Navigera till `Users and Groups`.
5. Klicka på `Add User`.
6. Välj eller Bjud in användaren för tilldelning till en speciell roll.
7. Välj rollen för användarna.
8. Tryck på `Assign` knappen.
9. Fortsätt att lägga `Administrator` till `Approver` Azure Key Vault åtkomst principer för användare i eller roll.

### <a name="remove-user"></a>Ta bort användare

1. Öppna Azure Portal på `portal.azure.com`.
2. Navigera till `Azure Active Directory`/`Enterprise applications`.
3. Välj registreringen av mikrotjänsten OPC Vault som standard `resourceGroupName-service`.
4. Navigera till `Users and Groups`.
5. Välj en användare med en roll som ska tas bort.
6. Tryck på `Remove` knappen.
7. Ta bort borttagna administratörer och god kännare även från Azure Key Vault-principer.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Lägg till princip för användar åtkomst i Azure Key Vault

Ytterligare åtkomst principer krävs för **god kännare** och **Administratörer**.

Som standard har tjänst identiteten endast begränsade behörigheter att komma åt Key Vault för att förhindra utökade åtgärder eller ändringar som ska utföras utan personifiering av användare. De grundläggande tjänst behörigheterna `Get` är `List` och för både hemligheter och certifikat. För hemligheter finns det bara ett undantag, tjänsten kan ha en `Delete` privat nyckel från den hemliga lagrings platsen när den har godkänts av en användare. Alla andra åtgärder kräver personifierade behörigheter för användaren.<br>

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>För en **god kännare-roll** måste följande behörigheter läggas till i Key Vault:

1. Öppna Azure Portal på `portal.azure.com`.
2. Navigera till OPC-valvet `resourceGroupName`som används under distributionen.
3. Navigera till Key Vault `resourceGroupName-xxxxx`.
4. Navigera till `Access Policies`.
5. Klicka på `Add new`.
6. Hoppa över mallen, det finns ingen mall som matchar kraven.
7. Klicka på `Select Principal` på och välj den användare som ska läggas till eller bjuda in en ny användare till klienten.
8. Kontrol `Key permissions`lera `Get`: ochviktigast.`List` `Sign`
9. Kontrol `Secret permissions`lera `Get`: ,`List` ,`Set` och .`Delete`
10. Kontrol `Certificate permissions`lera `Get`: och`List`.
11. Klicka på `Ok`.
12. `Save`något.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>För en **Administratörs roll** måste följande behörigheter läggas till i Key Vault:

1. Öppna Azure Portal på `portal.azure.com`.
2. Navigera till OPC-valvet `resourceGroupName`som används under distributionen.
3. Navigera till Key Vault `resourceGroupName-xxxxx`.
4. Navigera till `Access Policies`.
5. Klicka på `Add new`.
6. Hoppa över mallen, det finns ingen mall som matchar kraven.
7. Klicka på `Select Principal` på och välj den användare som ska läggas till eller bjuda in en ny användare till klienten.
8. Kontrol `Key permissions`lera `Get`: ochviktigast.`List` `Sign`
9. Kontrol `Secret permissions`lera `Get`: ,`List` ,`Set` och .`Delete`
10. Kontrol `Certificate permissions`lera `Get`:, ,`Update`, och`Create` . `List``Import`
11. Klicka på `Ok`.
12. `Save`något.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Ta bort princip för användar åtkomst från Azure Key Vault

1. Öppna Azure Portal på `portal.azure.com`.
2. Navigera till OPC-valvet `resourceGroupName`som används under distributionen.
3. Navigera till Key Vault `resourceGroupName-xxxxx`.
4. Navigera till `Access Policies`.
5. Hitta användaren som du vill ta bort och `... / Delete` Klicka på för att ta bort användar åtkomst.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar certifikat och användare i OPC-valvet rekommenderar vi följande steg:

> [!div class="nextstepaction"]
> [Säker kommunikation av OPC-enheter](howto-opc-vault-secure.md)
