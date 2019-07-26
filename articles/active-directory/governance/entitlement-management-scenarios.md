---
title: Vanliga scenarier i hantering av Azure AD-rättigheter (för hands version) – Azure Active Directory
description: Lär dig mer om de övergripande steg som du bör följa för vanliga scenarier i Azure Active Directory hantering av rättigheter (för hands version).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8e7709abcc97baac9bf657b9fff2110cb2e72c1
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489034"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Vanliga scenarier i hantering av Azure AD-rättigheter (för hands version)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättighets hantering är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Det finns flera sätt som du kan konfigurera rättighets hantering för din organisation. Men om du precis är igång är det bra att förstå vanliga scenarier för administratörer, god kännare och beställare.

## <a name="administrators"></a>Administratörer

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>Jag är nybörjare på hantering av rättigheter och jag vill ha hjälp med att komma igång

> [!div class="mx-tableFixed"]
> | Steg | Exempel |
> | --- | --- |
> | [Följ själv studie kursen för att skapa ditt första Access-paket](entitlement-management-access-package-first.md) | [![Azure Portal ikon](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Jag vill tillåta användare i min katalog att begära åtkomst till grupper, program eller SharePoint-webbplatser

> [!div class="mx-tableFixed"]
> | Steg | Exempel |
> | --- | --- |
> | **1.** [Skapa ett nytt Access-paket i en katalog](entitlement-management-access-package-create.md#start-new-access-package) | ![Skapa ett Access-paket](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Lägg till resurs roller för åtkomst till paket](entitlement-management-access-package-edit.md#add-resource-roles)<ul><li>Grupper</li><li>Program</li><li>SharePoint-webbplatser</li></ul> | ![Lägg till resurs roller](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Lägg till en princip](entitlement-management-access-package-edit.md#policy-for-users-in-your-directory)<ul><li>För användare i din katalog</li><li>Godkännande krävs</li><li>Förfalloinställningar</li></ul> | ![Lägg till princip](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-directory-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Jag vill tillåta användare från min affärs partner katalog (inklusive användare som ännu inte finns i min katalog) för att begära åtkomst till grupper, program eller SharePoint-webbplatser

> [!div class="mx-tableFixed"]
> | Steg | Exempel |
> | --- | --- |
> | **1.** [Skapa ett nytt Access-paket i en katalog](entitlement-management-access-package-create.md#start-new-access-package) | ![Skapa ett Access-paket](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Lägg till resurs roller för åtkomst till paket](entitlement-management-access-package-edit.md#add-resource-roles) | ![Lägg till resurs roller](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Lägg till en princip för externa användare](entitlement-management-access-package-edit.md#policy-for-users-not-in-your-directory)<ul><li>För användare som inte är i din katalog</li><li>Godkännande krävs</li><li>Förfalloinställningar</li></ul> | ![Lägg till princip för externa användare](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [Skicka länken till min åtkomst Portal för att begära åtkomst paketet till din företags partner](entitlement-management-access-package-edit.md#copy-my-access-portal-link)<ul><li>Företags partner kan dela länkar med sina användare</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>Jag vill ändra grupper, program eller SharePoint-webbplatser i ett Access-paket

> [!div class="mx-tableFixed"]
> | Steg | Exempel |
> | --- | --- |
> | **1.** Öppna Access-paketet | ![Lägg till resurs roller](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Lägga till eller ta bort resurs roller](entitlement-management-access-package-edit.md#add-resource-roles) | ![Lägg till resurs roller](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>Jag vill visa vem som har en tilldelning till grupper, program eller SharePoint-webbplatser

> [!div class="mx-tableFixed"]
> | Steg | Exempel |
> | --- | --- |
> | **1.** Öppna ett Access-paket | ![Lägg till resurs roller](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Visa tilldelningar](entitlement-management-access-package-edit.md#view-who-has-an-assignment)<ul><li>Visa vilka användare som har åtkomst till ett Access-paket</li><li>Visa vilken användares åtkomst har upphört att gälla</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>Jag vill visa grupper, program eller SharePoint-webbplatser som en användare har åtkomst till

> [!div class="mx-tableFixed"]
> | Steg | Exempel |
> | --- | --- |
> | [Visa användar tilldelnings rapport](entitlement-management-reports.md)<ul><li>Visa när de begärde och vem som godkänt</li></ul> |  |

## <a name="approvers"></a>Godkännare

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>Jag vill godkänna begär Anden för åtkomst till grupper, program eller SharePoint-webbplatser

> [!div class="mx-tableFixed"]
> | Steg | Exempel |
> | --- | --- |
> | **1.** [Öppna begäran i min åtkomst Portal](entitlement-management-request-approve.md#open-request) | [![Ikonen min åtkomst Portal](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [Godkänn åtkomstbegäran](entitlement-management-request-approve.md#approve-or-deny-request) | ![Bevilja åtkomst](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>Begärande

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>Jag vill visa de grupper, program eller SharePoint-webbplatser som är tillgängliga för mig och begära åtkomst

> [!div class="mx-tableFixed"]
> | Steg | Exempel |
> | --- | --- |
> | **1.** [Logga in på portalen för åtkomst](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Ikonen min åtkomst Portal](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Hitta Access-paket |  |
> | **3.** [Begär åtkomst](entitlement-management-request-access.md#request-an-access-package) | ![Begär åtkomst](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>Jag är extern användare och vill begära åtkomst till grupper, program eller SharePoint-webbplatser med en direkt länk

> [!div class="mx-tableFixed"]
> | Steg | Exempel |
> | --- | --- |
> | **1.** [Hitta länken till min åtkomst portal som du fick](entitlement-management-access-package-edit.md#copy-my-access-portal-link) |  |
> | **2.** [Logga in på portalen för åtkomst](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Ikonen min åtkomst Portal](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [Begär åtkomst](entitlement-management-request-access.md#request-an-access-package) | ![Begär åtkomst till extern användare](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>Jag vill visa de grupper, program eller SharePoint-webbplatser som jag redan har åtkomst till

> [!div class="mx-tableFixed"]
> | Steg | Exempel |
> | --- | --- |
> | **1.** [Logga in på portalen för åtkomst](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Ikonen min åtkomst Portal](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Visa aktiva åtkomst paket |  |

## <a name="next-steps"></a>Nästa steg

- [Självstudier: Skapa ditt första Access-paket](entitlement-management-access-package-first.md)
- [Delegera aktiviteter](entitlement-management-delegate.md)
