---
title: Åtgärda problem med certifikat för Azure Stack | Microsoft Docs
description: Använda Azure Stack-beredskap layout för att granska och åtgärda problem med certifikat.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/19/2018
ms.author: sethm
ms.reviewer: unknown
ms.openlocfilehash: 2024c4d8a251ad2066332a403db3d7f8c3dce975
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354118"
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>Åtgärda vanliga problem med Azure Stack PKI-certifikat

Informationen i den här artikeln kan hjälpa dig att förstå och lösa vanliga problem med Azure Stack PKI-certifikat. Du kan identifiera problem när du använder verktyget Azure Stack-beredskap för installation för att [Validera Azure Stack PKI-certifikat](azure-stack-validate-pki-certs.md). Verktyget kontrollerar för att säkerställa att certifikat uppfyller kraven för PKI för ett Azure Stack-distributioner och Azure Stack hemlighet Rotation och loggas resultaten i en [report.json filen](azure-stack-validation-report.md).  

## <a name="pfx-encryption"></a>PFX-kryptering

**Fel** -PFX-kryptering är inte TripleDES SHA1.   

**Reparation** -exportera PFX-filer med **TripleDES SHA1** kryptering. Detta är standard för alla Windows 10-klienter när du exporterar från certifikat snapin-modulen eller med `Export-PFXCertificate`. 

## <a name="read-pfx"></a>Läsa PFX

**Varning** -lösenord skyddar bara privat information i certifikatet.  

**Reparation** -exportera PFX-filer med den valfria inställningen för **aktivera certifikatet sekretess**.  

**Fel** -ogiltig för PFX-filen.  

**Reparation** – exportera certifikatet med hjälp av stegen i [förbereda Azure Stack-PKI-certifikat för distribution av](azure-stack-prepare-pki-certs.md).

## <a name="signature-algorithm"></a>Signaturalgoritm

**Fel** -signaturalgoritmen är SHA1.   
 
**Reparation** – Följ stegen i Azure Stack certifikat för signering begäran genererades för att återskapa den certifikatsignering begäran (CSR) med signaturalgoritm för SHA256. Skicka sedan CSR till certifikatutfärdaren att utfärda det på nytt.

## <a name="private-key"></a>Privat nyckel

**Fel** -den privata nyckeln saknas eller innehåller inte den lokala datorn-attribut.  

**Reparation** – från den dator som genererats CSR, exportera certifikatet med hjälp av stegen i förbereda Azure Stack-PKI-certifikat för distribution. De här stegen omfattar export från lokala datorns certifikatarkiv.

## <a name="certificate-chain"></a>Certifikatkedjan

**Fel** -certifikatkedjan har inte slutförts.  

**Reparation** -certifikat ska innehålla en fullständig kedja. Exportera certifikatet med hjälp av stegen i [förbereda Azure Stack-PKI-certifikat för distribution av](azure-stack-prepare-pki-certs.md) och välja alternativet **inkludera om möjligt alla certifikat i certifieringssökvägen.**

## <a name="dns-names"></a>DNS-namn

**Fel** -DNSNameList på certifikatet inte innehåller namnet på Azure Stack slutpunkten eller en giltig jokertecken-matchning. Matchar jokertecken är endast giltiga för vänster namnområdet DNS-namn. Till exempel _*. region.domain.com_ är bara giltig för *portal.region.domain.com*, inte _*. table.region.domain.com_. 
 
**Reparation** -använda stegen i Azure Stack certifikat för signering begäran genererades för att återskapa CSR med rätt DNS-namn för att stödja Azure Stack-slutpunkter. Skicka CSR till en certifikatutfärdare och följ sedan stegen i [förbereda Azure Stack-PKI-certifikat för distribution](azure-stack-prepare-pki-certs.md) att exportera certifikatet från den dator som genereras i CSR.  

## <a name="key-usage"></a>Nyckelanvändning

**Fel** – nyckelanvändning saknar digitala signatur eller nyckelchiffrering eller utökad nyckelanvändning saknar server-autentisering eller klientautentisering.  

**Reparation** – Följ stegen i [Azure Stack certifikat signering begäran generation](azure-stack-get-pki-certs.md) att återskapa CSR med rätt nyckelanvändning attribut. Skicka CSR till certifikatutfärdaren och bekräfta att en certifikatmall inte skriver över nyckelanvändningen i begäran.

## <a name="key-size"></a>Nyckelstorlek

**Fel** -nyckel är mindre än 2048.

**Reparation** – Följ stegen i [Azure Stack certifikat signering begäran generation](azure-stack-get-pki-certs.md) att återskapa CSR med rätt nyckellängd (2048) och sedan skicka CSR till certifikatutfärdaren.

## <a name="chain-order"></a>Kedjans ordning

**Fel** -ordningen i certifikatkedjan är felaktig.  

**Reparation** – exportera certifikatet med hjälp av stegen i [förbereda Azure Stack-PKI-certifikat för distribution av](azure-stack-prepare-pki-certs.md) och välja alternativet **inkludera om möjligt alla certifikat i certifieringssökvägen.** Se till att endast lövcertifikatet har valts för export. 

## <a name="other-certificates"></a>Andra certifikat

**Fel** -den PFX-paketet innehåller certifikat som inte är lövcertifikatet eller en del av certifikatkedjan.  

**Reparation** – exportera certifikatet med hjälp av stegen i [förbereda Azure Stack-PKI-certifikat för distribution av](azure-stack-prepare-pki-certs.md), och väljer alternativet **inkludera om möjligt alla certifikat i certifieringssökvägen.** Se till att endast lövcertifikatet har valts för export.

## <a name="fix-common-packaging-issues"></a>Åtgärda vanliga problem med paketering

Den **AzsReadinessChecker** innehåller en helper-cmdlets som anropas `Repair-AzsPfxCertificate`, vilket kan importera och sedan exportera en PFX-filen till att åtgärda vanliga problem i paketering, inklusive: 
 - *PFX-kryptering* är inte TripleDES-SHA1
 - *Privat nyckel* saknar lokal dator-attribut.
 - *Certifikatkedjan* är ofullständig eller fel. Den lokala datorn måste innehålla certifikatkedjan om PFX-paketet inte.
 - *Andra certifikat*.
 
`Repair-AzsPfxCertificate` Det går inte att hjälpa att om du vill skapa en ny begäran om Certifikatsignering och ange ett certifikat. 

### <a name="prerequisites"></a>Förutsättningar

Följande krav måste vara uppfyllda på den dator där verktyget körs: 
 - Windows 10 eller Windows Server 2016, internet-anslutning.
 - PowerShell 5.1 eller senare. Du kan kontrollera din version genom att köra följande PowerShell-cmdlet och granska de *större* och *mindre* versioner:

   ```powershell
   $PSVersionTable.PSVersion
   ```
 - Konfigurera [PowerShell för Azure Stack](azure-stack-powershell-install.md). 
 - Ladda ned den senaste versionen av den [Microsoft Azure Stack-beredskap Checker](https://aka.ms/AzsReadinessChecker) verktyget.

### <a name="import-and-export-an-existing-pfx-file"></a>Importera och exportera en befintlig PFX-fil

1. Öppna en administrativ PowerShell-kommandotolk och kör sedan följande kommando för att installera AzsReadinessChecker på en dator som uppfyller kraven:
  
   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. Kör följande cmdlet för att ange lösenordet för PFX från PowerShell-Kommandotolken. Ersätt *PFXpassword* med det faktiska lösenordet:
 
   ```powershell
   $password = Read-Host -Prompt PFXpassword -AsSecureString
   ```

3. Kör följande för att exportera en ny PFX-fil från PowerShell-Kommandotolken:
   - För `-PfxPath`, ange sökvägen till PFX-filen som du arbetar med. I följande exempel är sökvägen `.\certificates\ssl.pfx`.
   - För `-ExportPFXPath`, ange platsen och namnet på PFX-filen för export. I följande exempel är sökvägen `.\certificates\ssl_new.pfx`:

   ```powershell
   Repair-AzsPfxCertificate -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
   ```  

4. Granska utdata för en lyckad när verktyget har slutförts:
 
   ```PowerShell
   Repair-AzsPfxCertificate v1.1809.1005.1 started.
   Starting Azure Stack Certificate Import/Export
   Importing PFX .\certificates\ssl.pfx into Local Machine Store
   Exporting certificate to .\certificates\ssl_new.pfx
   Export complete. Removing certificate from the local machine store.
   Removal complete.
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Repair-AzsPfxCertificate Completed
   ```

## <a name="next-steps"></a>Nästa steg

[Läs mer om Azure Stack-säkerhet](azure-stack-rotate-secrets.md)
