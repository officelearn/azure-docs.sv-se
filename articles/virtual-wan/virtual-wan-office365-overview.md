---
title: Office 365 kontrollplanet i Azure virtuellt WAN-nätverk
description: Läs mer om Office 365 kontrollplanet i virtuellt WAN-nätverk.
author: cherylmc
ms.service: virtual-wan
services: virtual-wan
ms.topic: article
ms.date: 9/24/2018
ms.author: cherylmc
ms.openlocfilehash: 815b91295540e93f8f0ffbc002fcf02ce1c13365
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992685"
---
# <a name="office-365-control-plane-in-virtual-wan"></a>Office 365 kontrollplanet i virtuellt WAN-nätverk

Virtuella WAN-kunder med utvalda SDWAN enheter kan konfigurera O365 Internet seminarium om principer för betrodda trafik i Azure-portalen. Detta gör att:
- O365-trafik till in Microsoft-nätverket nära användaren som ger bästa möjliga användarupplevelse.
- På så sätt undviker trafik tillbaka-dragkraften och hår pining, vilket sparar WAN-kostnaderna.
- Leverera på principer för O365-anslutning.

## <a name="faqs"></a>Vanliga frågor och svar
### <a name="what-is-the-customer-benefit"></a>Vad är kund-förmånen?
Den här funktionen i virtuellt WAN-nätverk kan kan kunder nu ange kategorier för Office 365-trafik som de litar på för direkt internet seminarium om. Detta betrodda O365 trafik kommer kringgå proxyservrar och dirigera direkt från platsen till den närmaste Microsoft-POP. Detta förhindrar trafik tillbaka-dragkraften och hår pining, vilket ger bästa användarupplevelsen och sparar WAN-kostnaderna. 

### <a name="what-are-the-office-365-traffic-categories"></a>Vad är Office 365-trafik kategorier?
Office 365-slutpunkter representerar nätverksadresser och undernät. Slutpunkter kan vara URL-adresser, IP-adresser eller IP-intervall. URL: er kan antingen vara ett fullständigt domännamn som *account.office.net*, eller en URL med jokertecken som **. office365.com*. Slutpunkter är indelad i tre kategorier – **optimera**, **Tillåt**, och **standard**, baserat på deras allvarlighetsgrad. Mer information om slutpunkt-kategorier finns [här](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles#BKMK_Categories).

### <a name="which-office-365-traffic-category-is-recommended-by-microsoft-for-direct-internet-breakout"></a>Vilken kategori för Office 365-trafik som rekommenderas av Microsoft för direkt internet seminarium om?
Den **optimera** kategori är de mest kritiska nätverksslutpunkter och är nödvändig för att kringgå SSL break och inspektera och andra nätverksenheter säkerhet. Den bör ha direkt Internet utgående nära användare. De här slutpunkterna representerar Office 365-scenarier som är den mest känsliga för prestanda, svarstid och tillgänglighet. Den här kategorin innehåller en liten (i storleksordningen ~ 10) uppsättning nyckel-URL: er och en definierad uppsättning IP-undernät dedikerad för core Office 365-arbetsbelastningar som till exempel Exchange Online, SharePoint Online, Skype för Business Online och Microsoft Teams. 

Den **Tillåt** kategori rekommenderas för direkt Internet utgående också. Tillåt nätverkstrafik kan tolerera vissa Nätverksfördröjningen dock. Slutpunkter kategorierna optimera och Tillåt alla finns i Microsofts datacenter och hanteras som en del av Office 365. Standard-kategori kan dirigeras till en standardplats Internet utgående och inte kräver direkt Internet utgående eller kringgå av SSL break och inspektera enheter.

### <a name="how-do-i-set-my-o365-policies-via-virtual-wan"></a>Hur ställer jag in min O365 villkorsprinciper via virtuellt WAN-nätverk?
Du kan aktivera villkorsprinciper via det **virtuellt WAN-nätverk** -> **inställningar** -> **Configuration** fliken. Härifrån kan du ange dina önskade kategorier av O365-trafik för direkt internet seminarium om.

![Konfigurera Office 365 kontrollplanet i virtuellt WAN-nätverk](media/virtual-wan-office365-overview/configure-office365-control-plane.png)

### <a name="how-does-this-work"></a>Hur fungerar detta?

1.  O365-trafiken kommer Microsoft network nära användaren som ger en optimal upplevelse.
2.  Principer för vägen används av SDWAN. Sedan kringgår security proxyservrar för de betrodda och utför lokal direkt seminarium om av dessa kategorier.
3.  Backend dragkraften och trafik hår pining undviks sparar WAN-kostnaderna.

### <a name="which-partner-devices-support-this-via-virtual-wan"></a>Vilka partner-enheter som stöder detta via virtuellt WAN-nätverk?
Citrix stöder för närvarande dessa principer via virtuellt WAN-nätverk.

### <a name="what-happens-to-the-remaining-categories-of-untrusted-o365-traffic"></a>Vad händer med de återstående kategorierna (ej betrodda) O365-trafik?
Återstående O365 trafiken följer kunderna internet trafik standardsökvägen.

### <a name="what-if-i-have-already-specified-my-o365-policies-via-my-sdwan-provider"></a>Vad händer om jag redan har angett min O365-principer via min SDWAN leverantör?
Om du anger principer via både SDWAN UX och Azure virtuellt WAN-nätverk, ha vilka principer som angetts i virtuellt WAN-nätverk företräde.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [virtuellt WAN-nätverk](virtual-wan-about.md).