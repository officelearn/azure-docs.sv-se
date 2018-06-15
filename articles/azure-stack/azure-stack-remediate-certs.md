---
title: Åtgärda problem med certifikat för Azure-stacken | Microsoft Docs
description: Använda Azure Stack beredskap layout för att granska och åtgärda problem med certifikat.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 0d2c4d848f861e4e07dbd0de4609344955ca26f7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937855"
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>Reparera vanliga problem för Azure-stacken PKI-certifikat
Informationen i den här artikeln kan hjälpa dig att förstå och lösa vanliga problem för Azure-stacken PKI-certifikat. Du kan identifiera problem när du använder Azure Stack beredskap förutsättningar för att [Validera Azure Stack PKI-certifikat](azure-stack-validate-pki-certs.md). Verktyget kontrollerar att certifikat uppfyller kraven för PKI för ett Azure-stacken distribution och Azure Stack hemlighet Rotation och loggar resultatet ett [report.json filen](azure-stack-validation-report.md).  

## <a name="read-pfx"></a>Läsa PFX
**Varning** -lösenord skyddar bara privat information i certifikatet.  
**Reparation** -rekommenderar vi att du exportera PFX-filer med den valfria inställningen för **aktivera certifikat sekretess**.  

**Fel** -PFX-filen ogiltig.  
**Reparation** – exportera certifikatet med hjälp av stegen i [förbereda Azure Stack PKI-certifikat för distribution av](azure-stack-prepare-pki-certs.md).

## <a name="signature-algorithm"></a>Signaturalgoritm
**Fel** -signaturalgoritm är SHA1.    
**Reparation** -Använd steg i Azure-stacken certifikat för signering begäran som genereras och återskapa den certifikatsignering begäran (CSR) med signaturen algoritmen för SHA256. Sedan skicka CSR till certifikatutfärdaren att återutfärda certifikatet.

## <a name="private-key"></a>Privat nyckel
**Fel** -den privata nyckeln saknas eller innehåller inte den lokala datorn attribut.  
**Reparation** - från den dator som genererade CSR, exportera certifikatet med hjälp av stegen i förbereda Azure Stack PKI-certifikat för distribution. Dessa steg omfattar export från lokala datorns certifikatarkiv.

## <a name="certificate-chain"></a>Certifikatkedja
**Fel** -certifikatkedjan har inte slutförts.  
**Reparation** -certifikat ska innehålla en fullständig certifikatkedja.  Exportera certifikatet med hjälp av stegen i [förbereda Azure Stack PKI-certifikat för distribution av](azure-stack-prepare-pki-certs.md) och väljer alternativet **inkludera om möjligt alla certifikat i certifieringssökvägen.**

## <a name="dns-names"></a>DNS-namn
**Fel** -DNSNameList på certifikatet inte innehåller namnet på Azure-stacken slutpunkten eller en giltig jokertecken matchning.  Matchar jokertecken är endast giltiga för det vänstra namnområdet för DNS-namnet. Till exempel _*. region.domain.com_ är endast giltig för *portal.region.domain.com*, inte _*. table.region.domain.com_.  
**Reparation** -använda stegen i Azure-stacken certifikat för signering begäran generation återskapa CSR med rätt DNS-namn för att stödja Azure Stack-slutpunkter. Skicka CSR till en certifikatutfärdare och följ stegen i [förbereda Azure Stack PKI-certifikat för distribution](azure-stack-prepare-pki-certs.md) att exportera certifikatet från den dator som skapas i representant.  

## <a name="key-usage"></a>Nyckelanvändning
**Fel** – nyckelanvändning saknar Digital signatur eller Server-autentisering eller klientautentisering saknas nyckelchiffrering orEnhanced nyckelanvändning.  
**Reparation** -Följ stegen i [Azure Stack certifikat signering begäran generation](azure-stack-get-pki-certs.md) återskapa CSR med rätt nyckelanvändning attribut.  Skicka CSR till certifikatutfärdaren och bekräfta att en certifikatmall inte skriver över nyckelanvändning i begäran.

## <a name="key-size"></a>Nyckelstorlek
**Fel** -nyckelstorlek är mindre än 2048    
**Reparation** -Följ stegen i [Azure Stack certifikat signering begäran generation](azure-stack-get-pki-certs.md) att återskapa CSR med rätt nyckellängd (2048) och skicka CSR till certifikatutfärdaren.

## <a name="chain-order"></a>Kedjans ordning
**Fel** -certifikatkedjan är felaktig.  
**Reparation** – exportera certifikatet med hjälp av stegen i [förbereda Azure Stack PKI-certifikat för distribution](azure-stack-prepare-pki-certs.md) och väljer alternativet **inkludera om möjligt alla certifikat i certifieringssökvägen.** Se till att endast lövcertifikatet har valts för export. 

## <a name="other-certificates"></a>Andra certifikat
**Fel** -den PFX-paketet innehåller certifikat som inte är löv certifikat eller en del av certifikatkedjan.  
**Reparation** – exportera certifikatet med hjälp av stegen i [förbereda Azure Stack PKI-certifikat för distribution](azure-stack-prepare-pki-certs.md), och markera alternativet **inkludera om möjligt alla certifikat i certifieringssökvägen.** Se till att endast lövcertifikatet har valts för export.

## <a name="fix-common-packaging-issues"></a>Lös vanliga problem med paketering
AzsReadinessChecker kan importera och exportera en PFX-fil för att lösa vanliga problem med paketering, inklusive: 
 - *Privat nyckel* saknar attribut för lokala dator.
 - *Certifikatkedjan* är ofullständig eller fel. (Den lokala datorn måste innehålla certifikatkedjan om PFX-paketet inte.) 
 - *Andra certifikat*.
AzsReadinessChecker inte dock få hjälp om du behöver att generera en ny CSR och återutfärda ett certifikat. 

### <a name="prerequisites"></a>Förutsättningar
Följande krav måste vara på plats på datorn där verktyget körs: 
 - Windows 10 eller Windows Server 2016 med internet-anslutning.
 - PowerShell 5.1 eller senare. Kör följande PowerShell-kommando för att kontrollera din version och granska sedan de *större* version och *mindre* versioner.

   > `$PSVersionTable.PSVersion`
 - Konfigurera [PowerShell för Azure-stacken](azure-stack-powershell-install.md). 
 - Hämta den senaste versionen av [Microsoft Azure-stacken beredskap Checker](https://aka.ms/AzsReadinessChecker) verktyget.

### <a name="import-and-export-an-existing-pfx-file"></a>Importera och exportera en befintlig PFX-fil
1. Öppna en administrativ PowerShell-Kommandotolken på en dator som uppfyller kraven, och kör sedan följande kommando för att installera AzsReadinessChecker  
   > `Install-Module Microsoft.AzureStack.ReadinessChecker- Force`

2. Kör följande om du vill ange PFX-lösenordet från PowerShell-Kommandotolken. Ersätt *PFXpassword* med det faktiska lösenordet. 
   > `$password = Read-Host -Prompt PFXpassword -AsSecureString`

3. Kör följande om du vill exportera en ny PFX-fil från PowerShell-Kommandotolken.
   - För *- PfxPath*, ange sökvägen till PFX-filen som du arbetar med.  I följande exempel är sökvägen *.\certificates\ssl.pfx*.
   - För *- ExportPFXPath*, ange platsen och namnet på PFX-filen för export.  I följande exempel är sökvägen *.\certificates\ssl_new.pfx*

   > `Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx`  

4. När verktyget har slutförts kan du granska utdata för lyckad: ![resultat](./media/azure-stack-remediate-certs/remediate-results.png)

## <a name="next-steps"></a>Nästa steg
[Lär dig mer om Azure Stack-säkerhet](azure-stack-rotate-secrets.md)