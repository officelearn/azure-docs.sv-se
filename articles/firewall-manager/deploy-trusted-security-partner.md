---
title: Distribuera en betrodd säkerhets partner för Azure Firewall Manager
description: Lär dig hur du distribuerar en betrodd säkerhet i Azure Firewall Manager med hjälp av Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: bcea9a8674e4b1979698b7d28eb4192172b0dc11
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931310"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>Distribuera en betrodd säkerhetspartner (förhandsversion)

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

*Betrodda säkerhets partner* i Azure Firewall Manager gör att du kan använda dina välkända, bästa SECaaS-tjänster (Security-as-a-Service) från tredje part för att skydda Internet åtkomsten för dina användare.

Mer information om vilka scenarier som stöds och rikt linjer för bästa praxis finns i [Vad är betrodda säkerhets partner (för hands version)?](trusted-security-partners.md).

De säkerhets partner som stöds är **ZScaler** och **iboss** för den här för hands versionen. Regioner som stöds är WestCentralUS, Usanorracentrala, väst, WestUS2 och öster.

## <a name="prerequisites"></a>Krav

> [!IMPORTANT]
> För hands versionen av Azure Firewall Manager måste aktive ras explicit med kommandot `Register-AzProviderFeature` PowerShell.

Kör följande kommandon från en PowerShell-kommandotolk:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
Det tar upp till 30 minuter innan funktions registreringen har slutförts. Kör följande kommando för att kontrol lera registrerings statusen:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Distribuera en säkerhets leverantör från tredje part i en ny hubb

1. Logga in på Azure Portal på https://portal.azure.com.
2. I **Sök**skriver du **Firewall Manager** och väljer den under **tjänster**.
3. Navigera till **komma igång**. Välj **skapa en säker virtuell hubb**. 
4. Ange prenumeration och resurs grupp, Välj en region som stöds och Lägg till din hubb och virtuella WAN-information. 
5. **Distribuera VPN-gateway** är aktiverat som standard. En VPN Gateway krävs för att distribuera en betrodd säkerhets partner i hubben. 
6. Välj **Nästa: Azure-brandvägg**
   > [!NOTE]
   > Betrodda säkerhets partner ansluter till din hubb med hjälp av VPN Gateway tunnlar. Om du tar bort VPN Gateway försvinner anslutningarna till dina betrodda säkerhets partner.
7. Om du vill distribuera Azure-brandväggen för att filtrera privat trafik tillsammans med leverantör från tredje part för att filtrera Internet trafik, väljer du en princip för Azure-brandväggen. Se de [scenarier som stöds](trusted-security-partners.md#key-scenarios).
8. Om du bara vill distribuera en säkerhetsprovider från tredje part i hubben väljer du **Azure Firewall: enabled/disabled** för att ange att den ska **inaktive ras**. 
9. Välj **Nästa: betrodda säkerhets partner**.
10. Välj **betrodd säkerhets partner** för att ställa in den på **aktive rad**. Välj en partner. 
11. Välj **Nästa**. 
12. Granska innehållet och välj sedan **skapa**.

Distributionen av VPN gateway kan ta mer än 30 minuter.

Kontrol lera att navet har skapats genom att gå till Azure Firewall Manager-> skyddade hubbar. Välj översikts sidan för hubb-> för att Visa partner namnet och status som **säkerhets anslutning väntar**.

När hubben har skapats och säkerhets partnern har kon figurer ATS fortsätter du med att ansluta säkerhets leverantören till hubben.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Distribuera en säkerhets leverantör från tredje part i en befintlig hubb

Du kan också välja en befintlig hubb i ett virtuellt WAN-nätverk och konvertera det till en *säker virtuell hubb*.

1. I **komma igång**väljer du **konvertera befintliga hubbar**.
2. Välj en prenumeration och en befintlig hubb. Följ resten av stegen för att distribuera en provider från tredje part i en ny hubb.

Kom ihåg att en VPN-gateway måste distribueras för att konvertera en befintlig hubb till en skyddad hubb med leverantörer från tredje part.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Konfigurera säkerhets leverantörer från tredje part för att ansluta till en skyddad hubb

Om du vill konfigurera tunnlar till den virtuella hubbens VPN Gateway behöver tredjepartsleverantörer åtkomst behörighet till din hubb. Det gör du genom att associera ett huvud namn för tjänsten med din prenumeration eller resurs grupp och bevilja åtkomst behörighet. Du måste sedan ge dessa autentiseringsuppgifter till tredje part med hjälp av portalen.

1. Skapa Azure Active Directory (AD) tjänstens huvud namn: du kan hoppa över omdirigerings-URL: en. 

   [Gör så här: Använd portalen för att skapa ett Azure AD-program och tjänstens huvud namn som har åtkomst till resurser](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. Lägg till behörigheter och omfattning för tjänstens huvud namn.
   [Gör så här: Använd portalen för att skapa ett Azure AD-program och tjänstens huvud namn som har åtkomst till resurser](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > Du kan begränsa åtkomsten till endast din resurs grupp för mer detaljerad kontroll.
3. Följ [ZScaler: Konfigurera en Microsoft Azure virtuella WAN-integrations](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration) instruktioner till:

   - Logga in på Partner portalen och Lägg till dina autentiseringsuppgifter för att ge den betrodda partner åtkomst till den skyddade hubben.
   - Synkronisera de virtuella hubbarna i Partner portalen och konfigurera tunneln till den virtuella hubben. Du kan göra det när dina autentiseringsuppgifter för Azure AD-autentisering har verifierats.
   
4. Du kan titta på statusen för att skapa tunnel på Azures virtuella WAN-portal i Azure. När tunnlarna visar **anslutna** i både Azure och partner portalen fortsätter du med nästa steg för att ställa in vägar för att välja vilka grenar och virtuella nätverk som ska skicka Internet trafik till partnern.

## <a name="configure-route-settings"></a>Konfigurera flödes inställningar

1. Bläddra till Azure Firewall Manager-> skyddade hubbar. 
2. Välj en hubb. Nu bör Hub- **statusen visas i** stället för en **väntande säkerhets anslutning**.

   Se till att leverantören av tredje part kan ansluta till hubben. Tunnlarna på VPN-gatewayen ska vara i **anslutet** tillstånd. Det här tillståndet är en mer reflekterande av anslutnings hälsan mellan hubben och partnern från tredje part, jämfört med tidigare status.
3. Välj hubben och navigera till **flödes inställningar**.

   När du distribuerar en tredjeparts-Provider i hubben konverteras hubben till en *säker virtuell hubb*. Detta säkerställer att leverantören av tredje part annonserar en router med 0.0.0.0/0 (standard) till hubben. VNet-anslutningar och platser som är anslutna till navet får dock inte den här vägen om du inte väljer vilka anslutningar som ska hämta den här standard vägen.
4. Under **Internet trafik**väljer du **VNet-till-Internet** eller **Branch-till-Internet** eller båda så att vägar konfigureras via den tredje parten.

   Detta anger bara vilken typ av trafik som ska dirigeras till hubben, men den påverkar inte vägarna på virtuella nätverk eller grenar än. Dessa vägar sprids inte till alla virtuella nätverk/grenar som är kopplade till hubben som standard.
5. Du måste välja **säkra anslutningar** och välja de anslutningar som vägarna ska ställas in på. Detta anger vilka virtuella nätverk/grenar som kan börja skicka Internet trafik till tredje parts-providern.
6. Från **väg inställningar**väljer du **säkra anslutningar** under Internet trafik och väljer sedan det VNet eller de grenar (*platser* i virtuella WAN-nätverk) som ska skyddas. Välj **säker Internet trafik**.
   ![säker Internet trafik](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Gå tillbaka till sidan hubbar. Hubbens **betrodda säkerhets partner** status bör nu **skyddas**.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Internet trafik för gren eller VNet via tjänst från tredje part

Sedan kan du kontrol lera om virtuella VNet-datorer eller filial platsen kan komma åt Internet och kontrol lera att trafiken flödar till tjänsten från tredje part.

När du har slutfört stegen för flödes inställningen skickas virtuella nätverk för virtuella datorer och avdelnings platserna en 0/0 till en tjänst väg från tredje part. Det går inte att RDP eller SSH till dessa virtuella datorer. Om du vill logga in kan du distribuera tjänsten [Azure skydds](../bastion/bastion-overview.md) i ett peer-kopplat VNet.

## <a name="next-steps"></a>Nästa steg

- [Självstudie: skydda ditt moln nätverk med för hands versionen av Azure Firewall Manager med hjälp av Azure Portal](secure-cloud-network.md)




