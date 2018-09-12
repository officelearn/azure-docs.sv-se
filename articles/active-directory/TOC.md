# [Dokumentation om Azure Active Directory](index.md)

# Översikt
## [Vad är Azure Active Directory?](fundamentals/active-directory-whatis.md)
## [Om Azures identitetshantering](fundamentals/identity-fundamentals.md)
## [Förstå Azure-identitetslösningar](fundamentals/understand-azure-identity-solutions.md)
## [Välj en hybrididentitetslösning](choose-hybrid-identity-solution.md)
## [Associera Azure-prenumerationer](fundamentals/active-directory-how-subscriptions-associated-directory.md)
## [Överväganden för lagring och data](fundamentals/active-directory-data-storage-eu.md)
## [Vanliga frågor och svar](fundamentals/active-directory-faq.md)
## [Nyheter](fundamentals/whats-new.md)


# Kom igång
## [Kom igång med Azure AD](fundamentals/get-started-azure-ad.md)
## [Registrera dig för Azure AD Premium](fundamentals/active-directory-get-started-premium.md)
## [Lägga till ett anpassat domännamn](fundamentals/add-custom-domain.md)
## [Konfigurera varumärkesexponering](fundamentals/customize-branding.md)
## [Lägga till användare i Azure AD](fundamentals/add-users-azure-active-directory.md)
## [Tilldela licenser till användare](fundamentals/license-users-groups.md)
## [Konfigurera lösenordsåterställning via självbetjäning](authentication/quickstart-sspr.md)
## [Lägg till din organisations sekretessinformation i Azure AD](active-directory-properties-area.md)


# Gör så här för att
## Planera och designa
### [Förstå Azure AD-arkitektur](fundamentals/active-directory-architecture.md)
### [Anspråksmappning i Azure Active Directory](active-directory-claims-mapping.md)
### [Distribuera en hybrididentitetslösning](active-directory-hybrid-identity-design-considerations-overview.md)
#### Fastställa krav
##### [Identitet](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [Katalogsynkronisering](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Flerfaktorautentisering](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Strategi för identitetslivscykel](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Planera för datasäkerhet](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Dataskydd](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Innehållshantering](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Åtkomstkontroll](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Incidenthantering](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Planera din identitetslivscykel
##### [Uppgifter](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Införandestrategi](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Nästa steg](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [Jämförelse av verktyg](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## Hantera användare
### [Lägga till nya användare i Azure AD](fundamentals/add-users-azure-active-directory.md)
### [Hantera användarprofiler](fundamentals/active-directory-users-profile-azure-portal.md)
### [Återställa användarlösenord](fundamentals/active-directory-users-reset-password-azure-portal.md)
### [Dela konton](active-directory-sharing-accounts.md)
### [Tilldela användare administrativa roller](fundamentals/active-directory-users-assign-role-azure-portal.md)
### [Lägga till gästanvändare från annan katalog (B2B)](b2b/what-is-b2b.md)
#### [Administratörer som lägger till B2B-användare](b2b/add-users-administrator.md)
#### [Informationsarbetare som lägger till B2B-användare](b2b/add-users-information-worker.md)
#### [API och anpassning](b2b/customize-invitation-api.md)
#### [Google-federation](b2b/google-federation.md)
#### [Kod och Azure PowerShell-exempel](b2b/code-samples.md)
#### [Exempel på registreringsportal för självbetjäning](b2b/self-service-portal.md)
#### [E-postinbjudan](b2b/invitation-email-elements.md)
#### [Inlösning av inbjudan](b2b/redemption-experience.md)
#### [Lägga till B2B-användare utan inbjudan](b2b/add-user-without-invite.md)
#### [Tillåta eller blockera inbjudningar](b2b/allow-deny-list.md)
#### [Villkorlig åtkomst för B2B](b2b/conditional-access.md)
#### [B2B-delningsprinciper](b2b/delegate-invitations.md)
#### [Lägg till en B2B-användare till en roll](b2b/add-guest-to-role.md)
#### [Dynamiska grupper och B2B-användare](b2b/use-dynamic-groups.md)
#### [Lämna en organisation](b2b/leave-the-organization.md)
#### [Granskning och rapporter](b2b/auditing-and-reporting.md)
#### [B2B för hybridorganisationer](b2b/hybrid-organizations.md)
##### [Bevilja B2B-användare åtkomst till lokala appar](b2b/hybrid-cloud-to-on-premises.md)
##### [Bevilja lokala användare åtkomst till molnappar](b2b/hybrid-on-premises-to-cloud.md)
#### [Extern delning av B2B och Office 365](b2b/o365-external-user.md)
#### [B2B-licensiering](b2b/licensing-guidance.md)
#### [Aktuella begränsningar](b2b/current-limitations.md)
#### [Vanliga frågor och svar](b2b/faq.md)
#### [Felsökning av B2B](b2b/troubleshoot.md)
#### [Förstå B2B-användaren](b2b/user-properties.md)
#### [Användartoken för B2B](b2b/user-token.md)
#### [B2B för Azure AD-integrerade appar](b2b/configure-saas-apps.md)
#### [Mappning av användaranspråk för B2B](b2b/claims-mapping.md)
#### [Jämför B2B-samarbete med B2C](b2b/compare-with-b2c.md)
#### [Få support för B2B](b2b/get-support.md)

## [Hantera grupper och medlemmar](fundamentals/active-directory-manage-groups.md)
### [Hantera grupper](fundamentals/active-directory-groups-create-azure-portal.md)
### [Hantera gruppinställningar](fundamentals/active-directory-groups-settings-azure-portal.md)
## [Hantera rapporter](reports-monitoring/overview-reports.md)
### [Inloggningsaktiviteter](reports-monitoring/concept-sign-ins.md)
### [Granska aktivitet](reports-monitoring/concept-audit-logs.md)
### [Användare i riskzonen](reports-monitoring/concept-user-at-risk.md)
### [Riskfyllda inloggningar](reports-monitoring/concept-risky-sign-ins.md)
### [Riskhändelser](reports-monitoring/concept-risk-events.md)
### [Övervaka loggar med Azure Monitor](reports-monitoring/overview-activity-logs-in-azure-monitor.md)
### [Vanliga frågor och svar](reports-monitoring/reports-faq.md)

### Uppgifter
#### [Konfigurera namngivna platser](active-directory-named-locations.md)
#### [Hitta aktivitetsrapporter](reports-monitoring/howto-find-activity-reports.md)
#### [Använda Azure AD Power BI-innehållspaket](reports-monitoring/howto-power-bi-content-pack.md)
#### [Åtgärda användare som har flaggats för risk](reports-monitoring/howto-remediate-users-flagged-for-risk.md)
#### [Dirigera aktivitetsloggar till en Azure-händelsehubb](reports-monitoring/quickstart-azure-monitor-stream-logs-to-event-hub.md)
#### [Arkivera aktivitetsloggar till ett Azure Storage-konto](reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
#### [Integrera aktivitetsloggar med Splunk med Azure Monitor](reports-monitoring/tutorial-integrate-activity-logs-with-splunk.md)
#### [Integrera aktivitetsloggar med SumoLogic med Azure Monitor](reports-monitoring/howto-integrate-activity-logs-with-sumologic.md)

### Referens
#### [Kvarhållning](reports-monitoring/reference-reports-data-retention.md)
#### [Svarstider](reports-monitoring/reference-reports-latencies.md)
#### [Granska aktivitetsreferens](reports-monitoring/reference-audit-activities.md)
#### [Felkoder för inloggningsaktivitet](reports-monitoring/reference-sign-ins-error-codes.md)
#### [Tolka schemat för spårningsloggen i Azure Monitor](reports-monitoring/reference-azure-monitor-audit-log-schema.md)
#### [Tolka schemat för inloggningsloggen i Azure Monitor](reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md)

### Felsöka
#### [Saknade data i Azure AD-aktivitetsloggar](reports-monitoring/troubleshoot-missing-audit-data.md)
#### [Data som saknas i nedladdningar](reports-monitoring/troubleshoot-missing-data-download.md)
#### [Fel i innehållspaket för Azure Active Directory-aktivitetsloggar](reports-monitoring/troubleshoot-content-pack.md)
#### [Fel i Azure AD Reporting-API:et](reports-monitoring/troubleshoot-graph-api.md)

### [Programmässig åtkomst](reports-monitoring/concept-reporting-api.md)
#### [Förutsättningar](reports-monitoring/howto-configure-prerequisites-for-reporting-api.md)
#### [Använda certifikat](reports-monitoring/tutorial-access-api-with-certificates.md)

## [Hantera lösenord](authentication/concept-sspr-howitworks.md)

## Hantera appar
### [Översikt](manage-apps/what-is-application-management.md)
### [Komma igång](manage-apps/plan-an-application-integration.md)
### [Självstudier om SaaS-appintegration](saas-apps/tutorial-list.md)

### [Användaretablering och -avetablering till SaaS-appar](manage-apps/user-provisioning.md) 
#### [Självstudier om appintegration](saas-apps/tutorial-list.md) 
#### [Automatisera etablering till SCIM-aktiverade appar](manage-apps/use-scim-to-provision-users-and-groups.md) 
#### [Anpassa attributmappningar](manage-apps/customize-application-attributes.md) 
#### [Skriva uttryck för attributmappningar](manage-apps/functions-for-customizing-application-data.md) 
#### [Använda omfångsfilter](manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) 
#### [Rapportera om automatisk användaretablering](manage-apps/check-status-user-account-provisioning.md) 
#### [Felsöka användaretablering](active-directory-application-provisioning-content-map.md) 

### [Få åtkomst till appar med App Proxy](manage-apps/application-proxy.md)
#### Kom igång
##### [Aktivera App Proxy](manage-apps/application-proxy-enable.md)
##### [Publicera appar](manage-apps/application-proxy-publish-azure-portal.md)
##### [Anpassade domäner](manage-apps/application-proxy-configure-custom-domain.md)
#### [Enkel inloggning](manage-apps/application-proxy-single-sign-on.md)
##### [Enkel inloggning med KCD](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
##### [Enkel inloggning med rubriker](manage-apps/application-proxy-configure-single-sign-on-with-ping-access.md)
##### [Enkel inloggning med lösenordsvalv](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)
#### Begrepp
##### [Anslutningsappar](manage-apps/application-proxy-connectors.md)
##### [Säkerhet](manage-apps/application-proxy-security.md)
##### [Nätverk](manage-apps/application-proxy-network-topology.md)


##### [Uppgradera från TMG eller UAG](manage-apps/application-proxy-migration.md)

#### Avancerade konfigurationer
##### [Publicera i separata nätverk](manage-apps/application-proxy-connector-groups.md)
##### [Proxyservrar](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)
##### [Anspråksmedvetna appar](manage-apps/application-proxy-configure-for-claims-aware-applications.md)
##### [Ursprungliga klientappar](manage-apps/application-proxy-configure-native-client-application.md)
##### [Tyst installation](manage-apps/application-proxy-register-connector-powershell.md)
##### [Anpassad startsida](manage-apps/application-proxy-configure-custom-home-page.md)
##### [Översätt infogade länkar](manage-apps/application-proxy-configure-hard-coded-link-translation.md)
##### [Jokertecken](manage-apps/application-proxy-wildcard.md)
##### [Ta bort personliga data](manage-apps/application-proxy-remove-personal-data.md)


#### Publicera genomgångar
##### [Fjärrskrivbord](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft Teams](manage-apps/application-proxy-integrate-with-teams.md)
##### [Tableau](manage-apps/application-proxy-integrate-with-tableau.md)
##### [Qlik](manage-apps/application-proxy-qlik.md)
#### [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 

#### [Felsöka](manage-apps/application-proxy-troubleshoot.md)

### Hantera företagets appar
#### [Lägga till ett program](manage-apps/add-application-portal.md)
#### [Visa klientorganisationsappar](manage-apps/view-applications-portal.md)
#### [Konfigurera enkel inloggning](manage-apps/configure-single-sign-on-portal.md)
#### [Tilldela användare](manage-apps/assign-user-or-group-access-portal.md)
#### [Anpassa profilering](manage-apps/change-name-or-logo-portal.md)
#### [Inaktivera användarinloggningar](manage-apps/disable-user-sign-in-portal.md)
#### [Ta bort användare](manage-apps/remove-user-or-group-access-portal.md)

#### [Hantera användarens kontoetablering](manage-apps/configure-automatic-user-provisioning-portal.md)

#### [Avancerad certifikatsignering för SAML-appar](manage-apps/certificate-signing-options.md)
#### [Dölja ett program från en användarmiljö](manage-apps/hide-application-from-user-portal.md)
### [Konfigurera automatisk acceleration för inloggning med hjälp av HRD-princip](manage-apps/configure-authentication-for-federated-users-portal.md)
### [Migrera AD FS-appar till Azure AD](manage-apps/migrate-adfs-apps-to-azure.md) 
### [Hantera åtkomst till appar](manage-apps/what-is-access-management.md)
#### [Åtkomst med enkel inloggning](manage-apps/what-is-single-sign-on.md)
#### [Certifikat för enkel inloggning](manage-apps/manage-certificates-for-federated-single-sign-on.md)
#### [Klientrestriktioner](manage-apps/tenant-restrictions.md)
#### [Använd SCIM-etablering av användare](manage-apps/use-scim-to-provision-users-and-groups.md)

### [Förstå medgivande i Azure AD-program](application-consent-experience.md)

### Felsöka



#### Åtkomstpanel
##### [Appen visas inte](manage-apps/access-panel-troubleshoot-application-not-appearing.md)
##### [Appen visas oväntat](manage-apps/access-panel-troubleshoot-unexpected-application.md)
##### [Det går inte att logga in](manage-apps/access-panel-troubleshoot-web-sign-in-problem.md)
##### [Fel vid installation av webbläsartillägg](manage-apps/access-panel-extension-problem-installing.md)
##### [Så här använder du appåtkomst via självbetjäning](manage-apps/access-panel-manage-self-service-access.md)
##### [Fel vid användning av appåtkomst via självbetjäning](manage-apps/access-panel-troubleshoot-self-service-access.md)

#### Lägga till en app
##### [Välj apptyp](manage-apps/choose-application-type.md)
##### [Vanliga problem – galleriappar](manage-apps/adding-gallery-app-common-problems.md)
##### [Vanliga problem – inte galleriappar](manage-apps/adding-non-gallery-app-common-problems.md)

#### Programproxy
##### [Problem med att visa appsida](manage-apps/application-proxy-page-appearance-broken-problem.md)
##### [För lång programinläsning](manage-apps/application-proxy-page-load-speed-problem.md)
##### [Länkar på programsidan fungerar inte](manage-apps/application-proxy-page-links-broken-problem.md)
##### [Vilka portar ska jag öppna för min app?](manage-apps/application-proxy-connectivity-ports-how-to.md)
##### [Ingen fungerande anslutningsapp i en grupp med anslutningsappar för min app](manage-apps/application-proxy-connectivity-no-working-connector.md)
##### [Konfigurera i administratörsportalen](manage-apps/application-proxy-config-how-to.md)
##### [Konfigurera enkel inloggning till min app](manage-apps/application-proxy-config-sso-how-to.md)
##### [Problem med att skapa en app i administratörsportalen](manage-apps/application-proxy-config-problem.md)
##### [Konfigurera begränsad Kerberos-delegering](manage-apps/application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
##### [Konfigurera med PingAccess](manage-apps/application-proxy-back-end-ping-access-how-to.md)
##### [Felmeddelandet "Det går inte att få åtkomst till det här företagsprogrammet"](manage-apps/application-proxy-sign-in-bad-gateway-timeout-error.md)
##### [Problem med att installera anslutningsappen för programproxyagenten](manage-apps/application-proxy-connector-installation-problem.md)


#### Programregistrering
##### [Ange fält för programobjektet](application-dev-registration-config-specific-application-property-how-to.md)
##### [Ändra standardinställningarna för livslängd för token](application-dev-registration-config-change-token-lifetime-how-to.md)

#### Autentisering
##### [Konfigurera slutpunkter](application-dev-registration-config-how-to.md)

#### Villkorlig åtkomst
##### [Kunden uppfyllde inte förhandskraven för enhetsregistrering](active-directory-conditional-access.md)
##### [Klienten blockeras på grund av felaktiga inställningar av principer för villkorlig åtkomst](active-directory-conditional-access-device-remediation.md)
##### [Hur och när träder regler utanför företagsnätverk i kraft?](https://aka.ms/calocation)
##### [Hur ökar jag antalet enheter som användaren har tillåtelse att registrera i Azure AD?](active-directory-azureadjoin-setup.md)
##### [Hur konfigurerar jag villkorlig åtkomst för Exchange Online?](https://aka.ms/csforexchange)
##### [Hur konfigurerar jag villkorlig åtkomst för Windows 7-enheter?](active-directory-conditional-access.md#device-based-conditional-access)
##### [Vilka program stöds med villkorlig åtkomst?](active-directory-conditional-access-supported-apps.md)

#### Hitta ett API
##### [Hitta ett API](application-dev-api-find-an-api-how-to.md)

#### Hantera åtkomst
##### [Tilldela användare och grupper till en app](manage-apps/methods-for-assigning-users-and-groups.md)
##### [Ta bort användaråtkomst till en app](manage-apps/methods-for-removing-user-access.md)
##### [Konfigurera apptilldelning via självbetjäning](manage-apps/manage-self-service-access.md)
##### [Oväntad tilldelad användare](manage-apps/ways-users-get-assigned-to-applications.md)
##### [Oväntad app i programlistan](manage-apps/application-types.md)

#### Appar för flera klienter
##### [Konfigurera en ny app](application-dev-setup-multi-tenant-app.md)
##### [Lägga till i appgalleriet](application-dev-registration-config-multi-tenant-application-add-to-gallery-how-to.md)

#### Behörigheter
##### [Välja behörigheter för ett API](application-dev-perms-for-given-api.md)
##### [Bevilja behörigheter till min app](application-dev-registration-config-grant-permissions-how-to.md)
##### [Delegerat/programbehörigheter](application-dev-delegated-and-app-perms.md)
##### [Programmedgivande](application-dev-consent-framework.md)

#### Etablering
##### [Hur lång tid det tar](manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md)
##### [Tar flera timmar – galleriapp](manage-apps/application-provisioning-when-will-provisioning-finish.md)
##### [Konfigurera användaretablering – galleriapp](manage-apps/application-provisioning-config-how-to.md)
##### [Problem med att konfigurera användaretablering – galleriapp](manage-apps/application-provisioning-config-problem.md)
##### [Problem med att spara autentiseringsuppgifterna för administratören när användaretablering konfigureras – galleriapp](manage-apps/application-provisioning-config-problem-storage-limit.md)
##### [Användare har inte etablerats – galleriapp](manage-apps/application-provisioning-config-problem-no-users-provisioned.md)
##### [Fel användare har etablerats – galleriapp](manage-apps/application-provisioning-config-problem-wrong-users-provisioned.md)

#### Enkel inloggning
##### [Välj en metod](manage-apps/single-sign-on-modes.md)
##### [Konfigurera](application-dev-registration-config-sso-how-to.md)
##### [Konfigurera externa – galleriappar](manage-apps/configure-federated-single-sign-on-gallery-applications.md)
##### [Vanliga problem vid konfiguration av externa – galleriappar](manage-apps/configure-federated-single-sign-on-gallery-applications-problems.md)
##### [Konfigurera externa – inte galleriappar](manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
##### [Vanliga problem vid konfiguration av externa – inte galleriappar](manage-apps/configure-federated-single-sign-on-non-gallery-applications-problems.md)
##### [Konfigurera lösenord – galleriappar](manage-apps/configure-password-single-sign-on-gallery-applications.md)
##### [Vanliga problem vid konfiguration av lösenord – galleriappar](manage-apps/configure-password-single-sign-on-gallery-applications-problems.md)
##### [Konfigurera lösenord – inte galleriappar](manage-apps/configure-password-single-sign-on-non-gallery-applications.md)
##### [Vanliga problem vid konfiguration av lösenord – inte galleriappar](manage-apps/configure-password-single-sign-on-non-gallery-applications-problems.md)

#### Problem med användarinloggning
##### [Oväntad fråga om medgivande](manage-apps/application-sign-in-unexpected-user-consent-prompt.md)
##### [Fel vid användarmedgivande](manage-apps/application-sign-in-unexpected-user-consent-error.md)
##### [Problem vid inloggning från den anpassade portalen](manage-apps/application-sign-in-other-problem-deeplink.md)
##### [Problem vid inloggning från åtkomstpanelen](manage-apps/application-sign-in-other-problem-access-panel.md)
##### [Fel på inloggningssidan för programmet](manage-apps/application-sign-in-problem-application-error.md)
##### [Problem med enkel inloggning med lösenord – inte galleriapp](manage-apps/application-sign-in-problem-password-sso-non-gallery.md)
##### [Problem med enkel inloggning med lösenord – galleriapp](manage-apps/application-sign-in-problem-password-sso-gallery.md)
##### [Problem med att logga in i en Microsoft-app](manage-apps/application-sign-in-problem-first-party-microsoft.md)
##### [Problem med extern enkel inloggning – inte galleriapp](manage-apps/application-sign-in-problem-federated-sso-non-gallery.md)
##### [Problem med extern enkel inloggning – galleriapp](manage-apps/application-sign-in-problem-federated-sso-gallery.md)
##### [Problem med egenutvecklad app](manage-apps/application-sign-in-problem-custom-dev.md)
##### [Problem med lokal app – programproxy](manage-apps/application-sign-in-problem-on-premises-application-proxy.md)

### [Utveckla appar](active-directory-applications-guiding-developers-for-lob-applications.md)
### [Dokumentbibliotek](active-directory-apps-index.md)

## Hantera din katalog
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Egna domännamn
#### [Snabbstart](fundamentals/add-custom-domain.md)
### [Administrera din katalog](fundamentals/active-directory-administer.md)
### [Företagsroaming](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Aktivera](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Grupprincipinställningar](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Windows 10-inställningar](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [Vanliga frågor och svar](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Felsöka](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [Integrera lokala identiteter med Azure AD Connect](./connect/active-directory-aadconnect.md)

### [Konfigurera livslängd för token](active-directory-configurable-token-lifetimes.md)

## Åtkomstgranskningar
### [Översikt över åtkomstgranskningar](active-directory-azure-ad-controls-access-reviews-overview.md)
### [Slutför en åtkomstgranskning](active-directory-azure-ad-controls-complete-access-review.md)
### [Skapa en åtkomstgranskning](active-directory-azure-ad-controls-create-access-review.md)
### [Så här utför du en åtkomstgranskning](active-directory-azure-ad-controls-perform-access-review.md)
### [Så här granskar du din åtkomst](active-directory-azure-ad-controls-how-to-review-your-access.md)
### [Gäståtkomst med åtkomstgranskningar](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
### [Hantera användaråtkomst med granskningar](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
### [Hantera program och kontroller](active-directory-azure-ad-controls-manage-programs-controls.md)
### [Hämta resultat för åtkomstgranskning](active-directory-azure-ad-controls-retrieve-access-review.md)

## [Användningsvillkor](active-directory-tou.md)

## Skydda dina identiteter

### Azure AD Identity Protection
#### [Översikt](identity-protection/overview.md)
#### [Aktivera](identity-protection/enable.md)
#### [Hitta sårbarheter](identity-protection/vulnerabilities.md)
#### [Riskhändelser](active-directory-identity-protection-risk-events.md)
#### [Meddelanden](identity-protection/notifications.md)
#### [Inloggning](identity-protection/flows.md)
#### [Simulera riskhändelser](identity-protection/playbook.md)
#### [Avblockera användare](identity-protection/howto-unblock-user.md)
#### [Vanliga frågor och svar](identity-protection/faqs.md)
#### [Ordlista](identity-protection/glossary.md)
#### [Microsoft Graph](identity-protection/graph-get-started.md)
### [Privileged Identity Management](privileged-identity-management/pim-configure.md?toc=%2fazure%2factive-directory%2ftoc.json)

## [Distribuera AD FS i Azure](active-directory-aadconnect-azure-adfs.md)
### [Hög tillgänglighet](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Ändra signaturens hashalgoritm](active-directory-federation-sha256-guidance.md)

## [Felsöka](fundamentals/active-directory-troubleshooting-support-howto.md)

## Distribuera Azure AD PoC (Proof of Concept)
### [PoC-strategibok: Introduktion](active-directory-playbook-intro.md)
### [PoC-strategibok: Ingredienser](active-directory-playbook-ingredients.md)
### [PoC-strategibok: Implementering](active-directory-playbook-implementation.md)
### [PoC-strategibok: Komponenter](active-directory-playbook-building-blocks.md)

# Referens
## [Kodexempel](https://azure.microsoft.com/resources/samples/?service=active-directory)
## [Azure PowerShell-cmdletar](/powershell/azure/overview)
## [Java API-referens](/java/api)
## [.NET-API](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)

# Relaterat
## [Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Azure AD för utvecklare](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/pim-configure.md)

# Resurser
## [Distributionsscheman för Azure AD](./fundamentals/active-directory-deployment-plans.md)
## [Azures feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Azure-översikt](https://azure.microsoft.com/roadmap/?category=security-identity)
## [MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Prissättning](https://azure.microsoft.com/pricing/details/active-directory/)
## [Priskalkylator](https://azure.microsoft.com/pricing/calculator/)
## [Tjänstuppdateringar](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
