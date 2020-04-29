---
title: Självstudie för Mass inbjudan av B2B-samarbets användare – Azure AD
description: I den här självstudien lär du dig hur du använder PowerShell och en CSV-fil för att skicka massinbjudningar till externa Azure AD B2B-samarbetsanvändare.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 04/13/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ef9172ca5d0961bb6de1949a61199ce1d6c1bff
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81603422"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>Självstudier: Massinbjuda Azure AD B2B-samarbetsanvändare

Om du använder Azure Active Directory (Azure AD) B2B-samarbete för att arbeta med externa partners, kan du bjuda in flera gästanvändare till din organisation samtidigt. I den här självstudien får du lära dig hur du använder Azure Portal för att skicka mass inbjudningar till externa användare. Närmare bestämt kan du göra följande:

> [!div class="checklist"]
> * Använd **massredigera användare** för att förbereda en fil med kommaavgränsade värden (. csv) med användar information och Inbjudnings inställningar
> * Ladda upp CSV-filen till Azure AD
> * Verifiera att användarna har lagts till i katalogen

Om du inte har Azure Active Directory skapar du ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

Du behöver två eller flera test-e-postkonton att skicka inbjudningar till. Kontona måste finnas utanför din organisation. Du kan använda vilken typ av konto som helst, t.ex. konton i sociala medier som gmail.com- eller outlook.com-adresser.

## <a name="invite-guest-users-in-bulk"></a>Bjud in gäst användare i bulk

1. Logga in på Azure Portal med ett konto som är en användar administratör i organisationen.
2. I navigerings fönstret väljer du **Azure Active Directory**.
3. Under **Hantera**väljer **du användare** > **Mass inbjudan**.
4. På sidan **massredigera användare** väljer du **Hämta** för att hämta en giltig CSV-fil med Inbjudnings egenskaper.

    ![Hämtnings knapp för Mass inbjudan](media/tutorial-bulk-invite/bulk-invite-button.png)

5. Öppna CSV-filen och Lägg till en rad för varje gäst användare. Obligatoriska värden är:

   * **E-postadress att bjuda in** – användaren som får en inbjudan

   * **URL för omdirigering** – URL: en dit den inbjudna användaren vidarebefordras efter att inbjudan har accepterats

    ![Exempel på en CSV-fil med gäst användare angivna](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > Använd inte kommatecken i det **anpassade Inbjudnings meddelandet** eftersom de hindrar meddelandet från att kunna parsas.

6. Spara filen.
7. På sidan **gruppbjuda in användare** går du till **överför CSV-filen**och bläddrar till filen. När du väljer filen startar valideringen av. csv-filen. 
8. När fil innehållet verifieras visas **filen har laddats upp**. Om det finns fel måste du åtgärda dem innan du kan skicka jobbet.
9. När din fil klarar valideringen väljer du **Skicka** för att starta den Azure Mass åtgärd som lägger till inbjudningarna. 
10. Om du vill visa jobb statusen väljer **du klicka här för att visa status för varje åtgärd**. Du kan också välja **Mass åtgärds resultat** i avsnittet **aktivitet** . Om du vill ha mer information om varje rad objekt i Mass åtgärden väljer du värdena under kolumnerna **# lyckades**, **# Failure**eller **Totalt antal förfrågningar** . Om fel inträffar visas orsaken till felet.

    ![Exempel på Mass åtgärds resultat](media/tutorial-bulk-invite/bulk-operation-results.png)

11. När jobbet har slutförts visas ett meddelande om att Mass åtgärden har slutförts.

## <a name="verify-guest-users-in-the-directory"></a>Verifiera gäst användare i katalogen

Kontrol lera att gäst användare som du har lagt till finns i katalogen antingen i Azure Portal eller med PowerShell.

### <a name="view-guest-users-in-the-azure-portal"></a>Visa gäst användare i Azure Portal

1. Logga in på Azure Portal med ett konto som är en användar administratör i organisationen.
2. I navigerings fönstret väljer du **Azure Active Directory**.
3. Under **Hantera** väljer du **Användare**.
4. Under **Visa**väljer du **gäst användare** och verifiera att de användare som du har lagt till visas.

### <a name="view-guest-users-with-powershell"></a>Visa gäst användare med PowerShell

Kör följande kommando:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

Du bör se de användare som du har bjudit in i listan, med en User Principal Name (UPN) i formatet *EmailAddress*#EXT #\@*Domain*. Till exempel *lstokes_fabrikam. com # ext #\@contoso.onmicrosoft.com*, där contoso.onmicrosoft.com är organisationen som du skickade inbjudningarna från.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort test användar kontona i katalogen i Azure Portal på sidan användare genom att markera kryss rutan bredvid gäst användaren och sedan välja **ta bort**. 

Du kan också köra följande PowerShell-kommando för att ta bort ett användar konto:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Exempelvis: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skickat massinbjudningar till gästanvändare utanför organisationen. Härnäst ska du få lära dig hur inlösningsprocessen för inbjudningar ser ut.

> [!div class="nextstepaction"]
> [Läs mer om inlösningsprocessen för Azure AD B2B-samarbetsinbjudningar](redemption-experience.md)
