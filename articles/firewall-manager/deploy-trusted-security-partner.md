---
title: Distribuera en betrodd säkerhetspartner för Azure Firewall Manager
description: Lär dig hur du distribuerar en betrodd Azure Firewall Manager-säkerhet med Azure-portalen.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: bcea9a8674e4b1979698b7d28eb4192172b0dc11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931310"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>Distribuera en betrodd säkerhetspartner (förhandsversion)

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

*Med betrodda säkerhetspartner* i Azure Firewall Manager kan du använda dina välbekanta, bäst i raser från tredje part om säkerhet som en tjänst (SECaaS) för att skydda Internetåtkomst för dina användare.

Mer information om scenarier och riktlinjer för bästa praxis finns i [Vad är betrodda säkerhetspartner (förhandsversion)?](trusted-security-partners.md).

De säkerhetspartner som stöds är **ZScaler** och **iboss** för den här förhandsversionen. Regioner som stöds är WestCentralUS, NorthCentralUS, WestUS, WestUS2 och EastUS.

## <a name="prerequisites"></a>Krav

> [!IMPORTANT]
> Förhandsversionen av Azure Firewall Manager `Register-AzProviderFeature` måste uttryckligen aktiveras med kommandot PowerShell.

Kör följande kommandon från en PowerShell-kommandotolk:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
Det tar upp till 30 minuter innan funktionsregistreringen är klar. Kör följande kommando för att kontrollera din registreringsstatus:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Distribuera en säkerhetsleverantör från tredje part i ett nytt nav

1. Logga in på Azure Portal på https://portal.azure.com.
2. Skriv **Brandväggshanteraren** i **Sök**och välj den under **Tjänster**.
3. Navigera till **Komma igång**. Välj **Skapa en säker virtuell hubb**. 
4. Ange prenumerations- och resursgrupp, välj en region som stöds och lägg till hubb- och virtuell WAN-information. 
5. **Distribuera VPN-gateway** är aktiverat som standard. En VPN-gateway krävs för att distribuera en betrodd säkerhetspartner i navet. 
6. Välj **nästa: Azure-brandvägg**
   > [!NOTE]
   > Betrodda säkerhetspartner ansluter till ditt nav med HJÄLP AV VPN Gateway-tunnlar. Om du tar bort VPN-gatewayen går anslutningarna till dina betrodda säkerhetspartner förlorade.
7. Om du vill distribuera Azure-brandväggen för att filtrera privat trafik tillsammans med tredjepartstjänstleverantör för att filtrera Internet-trafik väljer du en princip för Azure-brandväggen. Se [scenarier](trusted-security-partners.md#key-scenarios)som stöds .
8. Om du bara vill distribuera en säkerhetsprovider från tredje part i navet väljer du **Azure-brandvägg: Aktiverad/inaktiverad** för att ställa in den till **Inaktiverad**. 
9. Välj **Nästa: Betrodda säkerhetspartner**.
10. Välj **Betrodd säkerhetspartner** om du vill ange den till **Aktiverad**. Välj en partner. 
11. Välj **Nästa**. 
12. Granska innehållet och välj sedan **Skapa**.

VPN-gatewaydistributionen kan ta mer än 30 minuter.

Om du vill kontrollera att hubben har skapats navigerar du till Azure Firewall Manager->Secured Hubs. Välj sidan översiktsöversikt >för att visa partnernamnet och statusen som **väntande säkerhetsanslutning**.

När navet har skapats och säkerhetspartnern har konfigurerats fortsätter du att ansluta säkerhetsprovidern till navet.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Distribuera en säkerhetsprovider från tredje part i ett befintligt nav

Du kan också välja ett befintligt nav i ett virtuellt WAN och konvertera det till ett *säkert virtuellt nav*.

1. Välj **Konvertera befintliga hubbar i**Komma **igång**.
2. Välj en prenumeration och ett befintligt nav. Följ resten av stegen för att distribuera en tredjepartsleverantör i ett nytt nav.

Kom ihåg att en VPN-gateway måste distribueras för att konvertera ett befintligt nav till säkra nav med tredjepartsleverantörer.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Konfigurera säkerhetsleverantörer från tredje part för att ansluta till ett säkert nav

För att konfigurera tunnlar till vpn-gatewayen för det virtuella navet behöver tredjepartsleverantörer åtkomsträttigheter till din hubb. Det gör du genom att associera ett tjänsthuvudnamn med din prenumeration eller resursgrupp och bevilja åtkomsträttigheter. Du måste sedan ge dessa autentiseringsuppgifter till tredje part med hjälp av deras portal.

1. Skapa huvudnamn för Azure Active Directory (AD) : Du kan hoppa över omdirigerings-URL:en. 

   [Så här: Använd portalen för att skapa ett Azure AD-program och tjänsthuvudnamn som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. Lägg till åtkomsträttigheter och omfattning för tjänstens huvudnamn.
   [Så här: Använd portalen för att skapa ett Azure AD-program och tjänsthuvudnamn som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > Du kan begränsa åtkomsten till endast resursgruppen för mer detaljerad kontroll.
3. Följ [ZScaler: Konfigurera instruktioner för Microsoft Azure Virtual WAN-integrering](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration) till:

   - Logga in på partnerportalen och lägg till dina autentiseringsuppgifter för att ge den betrodda partnern åtkomst till din säkra hubb.
   - Synkronisera de virtuella hubbar i partnerportalen och konfigurera tunneln till det virtuella navet. Du kan göra det när dina Azure AD-autentiseringsuppgifter har validerats.
   
4. Du kan titta på statusen för att skapa tunnlar på Azure Virtual WAN-portalen i Azure. När tunnlarna visar **ansluten** på både Azure och partnerportalen fortsätter du med nästa steg för att ställa in vägar för att välja vilka grenar och virtuella nätverk som ska skicka Internet-trafik till partnern.

## <a name="configure-route-settings"></a>Konfigurera ruttinställningar

1. Bläddra till Azure Firewall Manager -> secured hubs. 
2. Välj ett nav. Hub-statusen ska nu visa **etablerad** i stället för **Väntande säkerhetsanslutning**.

   Se till att tredjepartsleverantören kan ansluta till navet. Tunnlarna på VPN-gatewayen ska vara i **anslutet** tillstånd. Det här tillståndet återspeglar mer anslutningshälsan mellan navet och tredjepartspartnern, jämfört med tidigare status.
3. Markera navet och navigera till **Ruttinställningar**.

   När du distribuerar en tredjepartsleverantör till navet konverteras navet till ett *säkert virtuellt nav*. Detta säkerställer att tredjepartsleverantören annonserar en 0.0.0.0/0 (standard) väg till navet. VNet-anslutningar och platser som är anslutna till navet får dock inte den här vägen om du inte väljer att delta i vilka anslutningar som ska hämta den här standardvägen.
4. Under **Internettrafik**väljer du **VNet-till-Internet** eller **Branch-to-Internet** eller båda vägarna konfigureras skicka via den tredje parten.

   Detta anger bara vilken typ av trafik som ska dirigeras till navet, men det påverkar inte vägarna på virtuella nätverk eller grenar ännu. Dessa vägar sprids inte till alla virtuella nätverk/grenar som är kopplade till navet som standard.
5. Du måste välja **säkra anslutningar** och välja de anslutningar som dessa vägar ska ställas in på. Detta indikerar vilka virtuella nätverk/grenar som kan börja skicka Internet-trafik till tredjepartsleverantören.
6. Välj **Säkra anslutningar** under Internet-trafik från **Ruttinställningar**och välj sedan det virtuella nätverk eller grenar *(platser* i Virtuellt WAN) som ska skyddas. Välj **Säker Internettrafik**.
   ![Säker internettrafik](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Navigera tillbaka till hubbarsidan. Hubbens **status för betrodda säkerhetspartner** bör nu **skyddas**.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Filial- eller VNet-internettrafik via tredjepartstjänst

Därefter kan du kontrollera om virtuella VNet-datorer eller filialplatsen kan komma åt Internet och verifiera att trafiken flödar till tredjepartstjänsten.

När du har avslutat stegen för ruttinställning skickas virtuella datorer för virtuella datorer och filialplatser en 0/0 till tjänstvägen från tredje part. Du kan inte RDP eller SSH i dessa virtuella datorer. Om du vill logga in kan du distribuera [Azure Bastion-tjänsten](../bastion/bastion-overview.md) i ett peer-amerikanskt nätverk.

## <a name="next-steps"></a>Nästa steg

- [Självstudiekurs: Skydda ditt molnnätverk med förhandsversionen av Azure Firewall Manager med Azure-portalen](secure-cloud-network.md)




