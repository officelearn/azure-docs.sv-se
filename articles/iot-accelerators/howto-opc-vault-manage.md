---
title: Hantera certifikattjänsten OPC Vault – Azure | Microsoft-dokument
description: Hantera OPC Vault-rotcertifikatutfärdaren och användarbehörigheter.
author: mregen
ms.author: mregen
ms.date: 8/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 890a25ed2cf11d657cad930815d78dbf968cc9f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71203650"
---
# <a name="manage-the-opc-vault-certificate-service"></a>Hantera certifikattjänsten OPC Vault

I den här artikeln beskrivs de administrativa uppgifterna för OPC Vault-certifikathanteringstjänsten i Azure. Den innehåller information om hur du förnyar Certifikatutfärdarecertifikat, hur du förnyar listan över återkallade certifikat (CRL) och hur du beviljar och återkallar användaråtkomst.

## <a name="create-or-renew-the-root-ca-certificate"></a>Skapa eller förnya rotcertifikatutfärdarcertifikatet

När du har distribuerat OPC Vault måste du skapa rotcertifikatutfärdaren. Utan ett giltigt Certifikatutfärdarcertifikat kan du inte signera eller utfärda programcertifikat. Se [certifikat](howto-opc-vault-secure-ca.md#certificates) för att hantera dina certifikat med rimliga, säkra livstider. Förnya ett Certifikatutfärdare efter halva sin livstid. När du förnyar bör du också tänka på att den konfigurerade livslängden för ett nyligen signerat programcertifikat inte bör överstiga livslängden för Certifikatutfärdarens certifikat.
> [!IMPORTANT]
> Administratörsrollen krävs för att skapa eller förnya Certifikatutfärdarens certifikat.

1. Öppna certifikattjänsten `https://myResourceGroup-app.azurewebsites.net`på och logga in.
2. Gå till **certifikatgrupper**.
3. Det finns en standardcertifikatgrupp i listan. Välj **Redigera**.
4. I **Redigera information om certifikatgrupp**kan du ändra ämnesnamnet och livslängden för certifikatutfärdaren och programcertifikaten. Ämnet och livstiderna bör endast ställas in en gång innan det första CA-certifikatet utfärdas. Livstidsändringar under åtgärder kan resultera i inkonsekventa livstider för utfärdade certifikat och CRL:er.
5. Ange ett giltigt ämne `CN=My CA Root, O=MyCompany, OU=MyDepartment`(till exempel ).<br>
   > [!IMPORTANT]
   > Om du ändrar ämne måste du förnya Utfärdarcertifikatet, annars misslyckas tjänsten med att signera programcertifikat. Ämnet för konfigurationen kontrolleras mot ämnet för det aktiva Utfärdarcertifikatet. Om ämnena inte matchar nekas certifikatsignering.
6. Välj **Spara**.
7. Om du stöter på ett "förbjudet" fel i det här läget har dina användaruppgifter inte administratörsbehörighet för att ändra eller skapa ett nytt rotcertifikat. Som standard har användaren som distribuerade tjänsten administratörs- och signeringsroller med tjänsten. Andra användare måste läggas till i rollerna Godkännare, Författare eller Administratör, beroende på vad som är lämpligt i azure Active Directory -programregistreringen (Azure Active Directory).
8. Välj **information**. Detta bör visa den uppdaterade informationen.
9. Välj **Förnya CA-certifikat om** du vill utfärda det första Utfärdarcertifikatet eller förnya Utfärdarcertifikatet. Välj sedan **OK**.
10. Efter några sekunder visas **Certifikatinformation**. Om du vill hämta det senaste CERTIFIKATET och CRL för distribution till dina OPC UA-program väljer du **Utfärdare** eller **Crl**.

Nu är OPC UA-certifikathanteringstjänsten redo att utfärda certifikat för OPC UA-program.

## <a name="renew-the-crl"></a>Förnya referenslaboratoriet

Förnyelse av crl är en uppdatering, som bör distribueras till programmen med jämna mellanrum. OPC UA-enheter, som stöder tillägget CRL Distribution Point X509, kan direkt uppdatera referensvärdet från slutpunkten för mikrotjänst. Andra OPC UA-enheter kan kräva manuella uppdateringar eller kan uppdateras med hjälp av GDS-serverknapptillägg (*) för att uppdatera förtroendelistorna med certifikat och CRL:er.

I följande arbetsflöde återkallas alla certifikatbegäranden i de borttagna tillstånden i de CRL:er som motsvarar det Utfärdarcertifikat för vilka de har utfärdats. Versionsnumret för referenslaboratoriet ökas med 1. <br>
> [!NOTE]
> Alla utfärdade CRL:er är giltiga fram till utgången av Utfärdarens CA-certifikat. Detta beror på att OPC UA-specifikationen inte kräver en obligatorisk, deterministisk distributionsmodell för CRL.

> [!IMPORTANT]
> Administratörsrollen krävs för att förnya utfärdar-CRL.

1. Öppna certifikattjänsten `https://myResourceGroup.azurewebsites.net`på och logga in.
2. Gå till sidan **Certifikatgrupper.**
3. Välj **information**. Detta bör visa aktuell certifikat- och crl-information.
4. Välj **Uppdatera CRL-återkallarlista (CRL)** om du vill utfärda en uppdaterad lista över återkallade certifikat för alla aktiva utfärdarcertifikat i OPC Vault-lagringen.
5. Efter några sekunder visas **Certifikatinformation**. Om du vill hämta det senaste CERTIFIKATET och CRL för distribution till dina OPC UA-program väljer du **Utfärdare** eller **Crl**.

## <a name="manage-user-roles"></a>Hantera användarroller

Du hanterar användarroller för OPC Vault-mikrotjänsten i Azure AD Enterprise Application. En detaljerad beskrivning av rolldefinitionerna finns i [Roller](howto-opc-vault-secure-ca.md#roles).

Som standard kan en autentiserade användare i klienten logga in tjänsten som läsare. Högre privilegierade roller kräver manuell hantering i Azure-portalen eller med hjälp av PowerShell.

### <a name="add-user"></a>Lägga till användare

1. Öppna Azure Portal.
2. Gå till **Azure Active Directory** > **Enterprise-program**.
3. Välj registrering av OPC Vault-mikrotjänsten (som standard din `resourceGroupName-service`).
4. Gå till **Användare och grupper**.
5. Välj **Lägg till användare**.
6. Välj eller bjud in användaren för tilldelning till en viss roll.
7. Välj rollen för användarna.
8. Välj **Tilldela**.
9. För användare i rollen Administratör eller godkännare fortsätter du att lägga till azure key vault-åtkomstprinciper.

### <a name="remove-user"></a>Ta bort användare

1. Öppna Azure Portal.
2. Gå till **Azure Active Directory** > **Enterprise-program**.
3. Välj registrering av OPC Vault-mikrotjänsten (som standard din `resourceGroupName-service`).
4. Gå till **Användare och grupper**.
5. Markera en användare med en roll att ta bort och välj sedan **Ta bort**.
6. För borttagna användare i rollen Administratör eller godkännare tar du även bort dem från Azure Key Vault-principer.

### <a name="add-user-access-policy-to-azure-key-vault"></a>Lägga till användaråtkomstprincip i Azure Key Vault

Ytterligare åtkomstprinciper krävs för godkännare och administratörer.

Som standard har tjänstidentiteten endast begränsad behörighet att komma åt Key Vault, för att förhindra att förhöjda åtgärder eller ändringar sker utan att användaren personifierar. De grundläggande tjänstbehörigheterna är Hämta och Lista, för både hemligheter och certifikat. För hemligheter finns det bara ett undantag: tjänsten kan ta bort en privat nyckel från den hemliga butiken när den har accepterats av en användare. Alla andra åtgärder kräver användarpersonifierade behörigheter.

#### <a name="for-an-approver-role-the-following-permissions-must-be-added-to-key-vault"></a>För en godkännareroll måste följande behörigheter läggas till i Key Vault

1. Öppna Azure Portal.
2. Gå till ditt `resourceGroupName`OPC Vault , som används under distributionen.
3. Gå till Key `resourceGroupName-xxxxx`Vault .
4. Gå till **åtkomstprinciper**.
5. Välj **Lägg till ny**.
6. Hoppa över mallen. Det finns ingen mall som matchar kraven.
7. Välj **Huvudansvarig**och välj den användare som ska läggas till eller bjud in en ny användare till klienten.
8. Markera följande **nyckelbehörigheter:** **Hämta**, **Lista**och **Signera**.
9. Markera följande **hemliga behörigheter:** **Hämta**, **Lista**, **Ange**och **Ta bort**.
10. Välj följande **certifikatbehörigheter**: **Hämta** och **lista**.
11. Välj **OK**och välj **Spara**.

#### <a name="for-an-administrator-role-the-following-permissions-must-be-added-to-key-vault"></a>För en administratörsroll måste följande behörigheter läggas till i Key Vault

1. Öppna Azure Portal.
2. Gå till ditt `resourceGroupName`OPC Vault , som används under distributionen.
3. Gå till Key `resourceGroupName-xxxxx`Vault .
4. Gå till **åtkomstprinciper**.
5. Välj **Lägg till ny**.
6. Hoppa över mallen. Det finns ingen mall som matchar kraven.
7. Välj **Huvudansvarig**och välj den användare som ska läggas till eller bjud in en ny användare till klienten.
8. Markera följande **nyckelbehörigheter:** **Hämta**, **Lista**och **Signera**.
9. Markera följande **hemliga behörigheter:** **Hämta**, **Lista**, **Ange**och **Ta bort**.
10. Markera följande **certifikatbehörigheter:** **Hämta**, **Lista**, **Uppdatera**, **Skapa**och **Importera**.
11. Välj **OK**och välj **Spara**.

### <a name="remove-user-access-policy-from-azure-key-vault"></a>Ta bort användaråtkomstprincip från Azure Key Vault

1. Öppna Azure Portal.
2. Gå till ditt `resourceGroupName`OPC Vault , som används under distributionen.
3. Gå till Key `resourceGroupName-xxxxx`Vault .
4. Gå till **åtkomstprinciper**.
5. Leta reda på vilken användare som ska tas bort och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar OPC Vault-certifikat och användare kan du:

> [!div class="nextstepaction"]
> [Säker kommunikation av OPC-enheter](howto-opc-vault-secure.md)
