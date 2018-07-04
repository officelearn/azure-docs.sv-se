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
### [Dela konton](active-directory-sharing-accounts.md)
### [Tilldela användare administrativa roller](fundamentals/active-directory-users-assign-role-azure-portal.md)
### [Återställa en borttagen användare](fundamentals/active-directory-users-restore.md)
### [Lägga till gästanvändare från annan katalog (B2B)](b2b/what-is-b2b.md)
#### [Administratörer som lägger till B2B-användare](b2b/add-users-administrator.md)
#### [Informationsarbetare som lägger till B2B-användare](b2b/add-users-information-worker.md)
#### [API och anpassning](b2b/customize-invitation-api.md)
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
### Hantera grupper
#### [Azure Portal](fundamentals/active-directory-groups-create-azure-portal.md)
#### [Azure AD PowerShell för Graph (v2)](users-groups-roles/groups-settings-v2-cmdlets.md)
#### [Azure AD PowerShell MSOnline](users-groups-roles/groups-settings-cmdlets.md)
### [Hantera gruppmedlemmar](fundamentals/active-directory-groups-members-azure-portal.md)
### [Hantera gruppägare](fundamentals/active-directory-accessmanagement-managing-group-owners.md)
### [Hantera gruppmedlemskap](fundamentals/active-directory-groups-membership-azure-portal.md)
### [Tilldela licenser med hjälp av grupper](fundamentals/active-directory-licensing-whatis-azure-portal.md)
#### [Tilldela licenser till en grupp](active-directory-licensing-group-assignment-azure-portal.md)
#### [Identifiera och åtgärda licensproblem i en grupp](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [Migrera enskilda licensierade användare till gruppbaserad licensiering](active-directory-licensing-group-migration-azure-portal.md)
#### [Migrera användare mellan produktlicenser](active-directory-licensing-group-product-migration.md)
#### [Fler scenarier för gruppbaserad licensiering](active-directory-licensing-group-advanced.md)
#### [Azure PowerShell-exempel för gruppbaserad licensiering](active-directory-licensing-ps-examples.md)
#### [Referens för produkter och tjänstplaner i Azure AD](active-directory-licensing-product-and-service-plan-reference.md)
### [Konfigurera förfallodatum för Office 365-grupper](active-directory-groups-lifecycle-azure-portal.md)
### [Framtvinga en namnprincip för grupper](groups-naming-policy.md)
### [Visa alla grupper](fundamentals/active-directory-groups-view-azure-portal.md)
### [Hantera gruppåtkomst till SaaS-appar](users-groups-roles/groups-saasapps.md)
### [Återställa en borttagen Office 365-grupp](fundamentals/active-directory-groups-restore-azure-portal.md)
### [Hantera gruppinställningar](fundamentals/active-directory-groups-settings-azure-portal.md) 
### Skapa avancerade regler
#### [Azure Portal](active-directory-groups-dynamic-membership-azure-portal.md)
### [Konfigurera självbetjäningsgrupper](users-groups-roles/groups-self-service-management.md)
### [Felsöka](users-groups-roles/groups-troubleshooting.md)

## [Hantera rapporter](active-directory-reporting-azure-portal.md)
### [Inloggningsaktiviteter](active-directory-reporting-activity-sign-ins.md)
### [Granska aktivitet](active-directory-reporting-activity-audit-logs.md)
### [Användare i riskzonen](active-directory-reporting-security-user-at-risk.md)
### [Riskfyllda inloggningar](active-directory-reporting-security-risky-sign-ins.md)
### [Riskhändelser](active-directory-reporting-risk-events.md)
### [Vanliga frågor och svar](active-directory-reporting-faq.md)
### Uppgifter
#### [Konfigurera namngivna platser](active-directory-named-locations.md)
#### [Hitta aktivitetsrapporter](active-directory-reporting-migration.md)
#### [Använd innehållspaketet för Azure Active Directory Power BI](active-directory-reporting-power-bi-content-pack-how-to.md)
#### [Åtgärda användare som har flaggats för risk](active-directory-report-security-user-at-risk-remediation.md)
### Referens
#### [Kvarhållning](active-directory-reporting-retention.md)
#### [Svarstider](active-directory-reporting-latencies-azure-portal.md)
#### [Granska aktivitetsreferens](active-directory-reporting-activity-audit-reference.md)
#### [Felkoder för inloggningsaktivitet](active-directory-reporting-activity-sign-ins-errors.md)
#### [Multi-Factor Authentication](active-directory-reporting-activity-sign-ins-mfa.md)



### Felsöka
#### [Granskningsdata som saknas](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [Data som saknas i nedladdningar](active-directory-reporting-troubleshoot-missing-data-download.md)
#### [Fel i innehållspaket för Azure Active Directory-aktivitetsloggar](active-directory-reporting-troubleshoot-content-pack.md)
### [Programmässig åtkomst](active-directory-reporting-api-getting-started-azure-portal.md)
#### [Förutsättningar](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [Granska exempel](active-directory-reporting-api-audit-samples.md)
#### [Inloggningsexempel](active-directory-reporting-api-sign-in-activity-samples.md)
#### [Använda certifikat](active-directory-reporting-api-with-certificates.md)

## Hantera lösenord
### [Lösenordsöversikt](authentication/active-directory-passwords-overview.md)
### Användardokument
#### [Återställ eller ändra ditt lösenord](active-directory-passwords-update-your-own-password.md)
#### [Metodtips för lösenord](active-directory-secure-passwords.md)
#### [Registrera för återställning av lösenord för självbetjäning](active-directory-passwords-reset-register.md)
### [SSPR – så här fungerar det](authentication/concept-sspr-howitworks.md)
### [SSPR-distributionsguide](authentication/howto-sspr-deployment.md)
### [SSPR och Windows 10](authentication/tutorial-sspr-windows.md)
### [SSPR-principer ](authentication/concept-sspr-policy.md)
### [SSPR-anpassning](authentication/concept-sspr-customization.md)
### [SSPR-datakrav](authentication/howto-sspr-authenticationdata.md)
### [SSPR-rapportering](authentication/howto-sspr-reporting.md)
### [Smart utlåsning](authentication/howto-password-smart-lockout.md)
### [Eliminera svaga lösenord](authentication/concept-password-ban-bad.md)
### [Konfigurera listan med förbjudna lösenord](authentication/howto-password-ban-bad.md)
### [Lokal integration](authentication/concept-password-ban-bad-on-premises.md)
### [Distribuera Azure AD-lösenordsskydd](authentication/howto-password-ban-bad-on-premises.md)
### [Konfigurera Azure AD-lösenordsskydd](authentication/howto-password-ban-bad-on-premises-operations.md)
### [Övervaka Azure AD-lösenordsskydd](authentication/howto-password-ban-bad-on-premises-troubleshoot.md)
### IT-administratörer: Återställ lösenord
#### [Azure Portal](fundamentals/active-directory-users-reset-password-azure-portal.md)
### [Licensiera SSPR](authentication/concept-sspr-licensing.md)
### [Tillbakaskrivning av lösenord](authentication/howto-sspr-writeback.md)
### [Felsöka](authentication/active-directory-passwords-troubleshoot.md)
### [Vanliga frågor och svar](authentication/active-directory-passwords-faq.md)


## Hantera enheter
### [Introduktion](device-management-introduction.md)
### [Använd Azure Portal](device-management-azure-portal.md)
### [Planera Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
### [Vanliga frågor och svar](device-management-faq.md)
### Uppgifter
#### [Konfigurera Azure AD-registrerade Windows 10-enheter](device-management-azuread-registered-devices-windows10-setup.md)
#### [Konfigurera Azure AD-anslutna enheter](device-management-azuread-joined-devices-setup.md)
#### [Konfigurera Azure AD-anslutna hybrid-enheter](device-management-hybrid-azuread-joined-devices-setup.md)
#### [Distribuera lokalt](active-directory-device-registration-on-premises-setup.md)
#### [Azure AD-anslutning under första körningen av Windows 10](device-management-azuread-joined-devices-frx.md)
### Felsöka
#### [Azure AD-anslutna Windows 10- och Windows Server 2016-hybridenheter](device-management-troubleshoot-hybrid-join-windows-current.md)
#### [Tidigare Azure AD-anslutna Windows-hybridenheter ](device-management-troubleshoot-hybrid-join-windows-legacy.md)

## Hantera appar
### [Översikt](manage-apps/what-is-application-management.md)
### [Komma igång](manage-apps/plan-an-application-integration.md)
### [Självstudier om SaaS-appintegration](saas-apps/tutorial-list.md)

### [Användaretablering och -avetablering till SaaS-appar](active-directory-saas-app-provisioning.md) 
#### [Självstudier om appintegration](saas-apps/tutorial-list.md) 
#### [Automatisera etablering till SCIM-aktiverade appar](manage-apps/use-scim-to-provision-users-and-groups.md) 
#### [Anpassa attributmappningar](active-directory-saas-customizing-attribute-mappings.md) 
#### [Skriva uttryck för attributmappningar](active-directory-saas-writing-expressions-for-attribute-mappings.md) 
#### [Använda omfångsfilter](active-directory-saas-scoping-filters.md) 
#### [Rapportera om automatisk användaretablering](active-directory-saas-provisioning-reporting.md) 
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
##### [Jokertecken](active-directory-application-proxy-wildcard.md)
##### [Ta bort personliga data](manage-apps/application-proxy-remove-personal-data.md)


#### Publicera genomgångar
##### [Fjärrskrivbord](manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
##### [SharePoint](manage-apps/application-proxy-integrate-with-sharepoint-server.md)
##### [Microsoft Teams](manage-apps/application-proxy-integrate-with-teams.md)
##### [Tableau](manage-apps/application-proxy-integrate-with-tableau.md)
##### [Qlik](active-directory-application-proxy-qlik.md)
#### [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 

#### [Felsöka](manage-apps/application-proxy-troubleshoot.md)

### Hantera företagets appar
#### [Tilldela användare](manage-apps/assign-user-or-group-access-portal.md)
#### [Anpassa profilering](manage-apps/change-name-or-logo-portal.md)
#### [Inaktivera användarinloggningar](manage-apps/disable-user-sign-in-portal.md)
#### [Ta bort användare](manage-apps/remove-user-or-group-access-portal.md)
#### [Visa alla mina appar](manage-apps/view-applications-portal.md)
#### [Hantera användarens kontoetablering](manage-apps/configure-automatic-user-provisioning-portal.md)
#### [Hantera enkel inloggning för företagsappar](manage-apps/configure-single-sign-on-portal.md)
#### [Avancerad certifikatsignering för SAML-appar](manage-apps/certificate-signing-options.md)
#### [Dölja ett program från en användarmiljö](manage-apps/hide-application-from-user-portal.md)
### [Konfigurera automatisk acceleration för inloggning med hjälp av HRD-princip](manage-apps/configure-authentication-for-federated-users-portal.md)
### [Migrera AD FS-appar till Azure AD](manage-apps/migrate-adfs-apps-to-azure.md) 
### [Hantera åtkomst till appar](manage-apps/what-is-access-management.md)
#### [Åtkomst med enkel inloggning](manage-apps/what-is-single-sign-on.md)
#### [Certifikat för enkel inloggning](manage-apps/manage-certificates-for-federated-single-sign-on.md)
#### [Klientrestriktioner](manage-apps/tenant-restrictions.md)
#### [Använd SCIM-etablering av användare](manage-apps/use-scim-to-provision-users-and-groups.md)


### Felsöka



#### Åtkomstpanel
##### [Appen visas inte](application-access-panel-unexpected-application-not-appearing.md)
##### [Appen visas oväntat](application-access-panel-unexpected-application-appears.md)
##### [Det går inte att logga in](application-access-panel-web-sign-in-problem.md)
##### [Fel vid installation av webbläsartillägg](application-access-panel-extension-problem-installing.md)
##### [Så här använder du appåtkomst via självbetjäning](application-access-panel-self-service-applications-how-to.md)
##### [Fel vid användning av appåtkomst via självbetjäning](application-access-panel-self-service-applications-problem.md)

#### Lägga till en app
##### [Välj apptyp](application-config-add-app-problem-how-to-choose-application-type.md)
##### [Vanliga problem – galleriappar](application-config-add-app-problem-problem-adding-gallery-app.md)
##### [Vanliga problem – inte galleriappar](application-config-add-app-problem-problem-adding-non-gallery-app.md)

#### Programproxy
##### [Problem med att visa appsida](application-proxy-page-appearance-broken-problem.md)
##### [För lång programinläsning](application-proxy-page-load-speed-problem.md)
##### [Länkar på programsidan fungerar inte](application-proxy-page-links-broken-problem.md)
##### [Vilka portar ska jag öppna för min app?](application-proxy-connectivity-ports-how-to.md)
##### [Ingen fungerande anslutningsapp i en grupp med anslutningsappar för min app](application-proxy-connectivity-no-working-connector.md)
##### [Konfigurera i administratörsportalen](application-proxy-config-how-to.md)
##### [Konfigurera enkel inloggning till min app](application-proxy-config-sso-how-to.md)
##### [Problem med att skapa en app i administratörsportalen](application-proxy-config-problem.md)
##### [Konfigurera begränsad Kerberos-delegering](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
##### [Konfigurera med PingAccess](application-proxy-back-end-ping-access-how-to.md)
##### [Felmeddelandet "Det går inte att få åtkomst till det här företagsprogrammet"](application-proxy-sign-in-bad-gateway-timeout-error.md)
##### [Problem med att installera anslutningsappen för programproxyagenten](application-proxy-connector-installation-problem.md)


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
##### [Tilldela användare och grupper till en app](application-access-assignment-how-to-add-assignment.md)
##### [Ta bort användaråtkomst till en app](application-access-assignment-how-to-remove-assignment.md)
##### [Konfigurera apptilldelning via självbetjäning](application-access-self-service-how-to.md)
##### [Oväntad tilldelad användare](application-access-unexpected-user-assignment.md)
##### [Oväntad app i programlistan](application-access-unexpected-application.md)

#### Appar för flera klienter
##### [Konfigurera en ny app](application-dev-setup-multi-tenant-app.md)
##### [Lägga till i appgalleriet](application-dev-registration-config-multi-tenant-application-add-to-gallery-how-to.md)

#### Behörigheter
##### [Välja behörigheter för ett API](application-dev-perms-for-given-api.md)
##### [Bevilja behörigheter till min app](application-dev-registration-config-grant-permissions-how-to.md)
##### [Delegerat/programbehörigheter](application-dev-delegated-and-app-perms.md)
##### [Programmedgivande](application-dev-consent-framework.md)

#### Etablering
##### [Hur lång tid det tar](application-provisioning-when-will-provisioning-finish-specific-user.md)
##### [Tar flera timmar – galleriapp](application-provisioning-when-will-provisioning-finish.md)
##### [Konfigurera användaretablering – galleriapp](application-provisioning-config-how-to.md)
##### [Problem med att konfigurera användaretablering – galleriapp](application-provisioning-config-problem.md)
##### [Problem med att spara autentiseringsuppgifterna för administratören när användaretablering konfigureras – galleriapp](application-provisioning-config-problem-storage-limit.md)
##### [Användare har inte etablerats – galleriapp](application-provisioning-config-problem-no-users-provisioned.md)
##### [Fel användare har etablerats – galleriapp](application-provisioning-config-problem-wrong-users-provisioned.md)

#### Enkel inloggning
##### [Välj en metod](application-config-sso-how-to-choose-sign-on-method.md)
##### [Konfigurera](application-dev-registration-config-sso-how-to.md)
##### [Konfigurera externa – galleriappar](application-config-sso-how-to-configure-federated-sso-gallery.md)
##### [Vanliga problem vid konfiguration av externa – galleriappar](application-config-sso-problem-configure-federated-sso-gallery.md)
##### [Konfigurera externa – inte galleriappar](application-config-sso-how-to-configure-federated-sso-non-gallery.md)
##### [Vanliga problem vid konfiguration av externa – inte galleriappar](application-config-sso-problem-configure-federated-sso-non-gallery.md)
##### [Konfigurera lösenord – galleriappar](application-config-sso-how-to-configure-password-sso-gallery.md)
##### [Vanliga problem vid konfiguration av lösenord – galleriappar](application-config-sso-problem-configure-password-sso-gallery.md)
##### [Konfigurera lösenord – inte galleriappar](application-config-sso-how-to-configure-password-sso-non-gallery.md)
##### [Vanliga problem vid konfiguration av lösenord – inte galleriappar](application-config-sso-problem-configure-password-sso-non-gallery.md)

#### Problem med användarinloggning
##### [Oväntad fråga om medgivande](application-sign-in-unexpected-user-consent-prompt.md)
##### [Fel vid användarmedgivande](application-sign-in-unexpected-user-consent-error.md)
##### [Problem vid inloggning från den anpassade portalen](application-sign-in-other-problem-deeplink.md)
##### [Problem vid inloggning från åtkomstpanelen](application-sign-in-other-problem-access-panel.md)
##### [Fel på inloggningssidan för programmet](application-sign-in-problem-application-error.md)
##### [Problem med enkel inloggning med lösenord – inte galleriapp](application-sign-in-problem-password-sso-non-gallery.md)
##### [Problem med enkel inloggning med lösenord – galleriapp](application-sign-in-problem-password-sso-gallery.md)
##### [Problem med att logga in i en Microsoft-app](application-sign-in-problem-first-party-microsoft.md)
##### [Problem med extern enkel inloggning – inte galleriapp](application-sign-in-problem-federated-sso-non-gallery.md)
##### [Problem med extern enkel inloggning – galleriapp](application-sign-in-problem-federated-sso-gallery.md)
##### [Problem med egenutvecklad app](application-sign-in-problem-custom-dev.md)
##### [Problem med lokal app – programproxy](application-sign-in-problem-on-premises-application-proxy.md)

### [Utveckla appar](active-directory-applications-guiding-developers-for-lob-applications.md)
### [Dokumentbibliotek](active-directory-apps-index.md)

## Hantera din katalog
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Egna domännamn
#### [Snabbstart](fundamentals/add-custom-domain.md)
#### [Lägga till anpassade domännamn](users-groups-roles/domains-manage.md)
### [Administrera din katalog](fundamentals/active-directory-administer.md)
### [Ta bort en katalog](directory-delete-howto.md)
### [Flera kataloger](active-directory-licensing-directory-independence.md)
### [Registrering av självbetjäning](active-directory-self-service-signup.md)
### [Ta över en ohanterad katalog](domains-admin-takeover.md)
### [Företagsroaming](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Aktivera](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Grupprincipinställningar](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Windows 10-inställningar](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [Vanliga frågor och svar](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Felsöka](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [Integrera lokala identiteter med Azure AD Connect](./connect/active-directory-aadconnect.md)

## Delegera åtkomst till resurser
### [Visa medlemmar av en administratörsroll](directory-manage-roles-portal.md)
### [Administratörsroller](users-groups-roles/directory-assign-admin-roles.md)
#### [Tilldela administratörsroll till en användare](fundamentals/active-directory-users-assign-role-azure-portal.md)
#### [Jämföra behörigheter för medlemmar och gästanvändare](fundamentals/users-default-permissions.md)
### [Förstärka säkerheten för administratörsrollen](admin-roles-best-practices.md)  
#### [Skapa administratörskonton för åtkomst vid akutfall](users-groups-roles/directory-emergency-access.md)
### [Administrativa enheter](users-groups-roles/directory-administrative-units.md)
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

## Skydda dina identiteter
### [Villkorlig åtkomst](active-directory-conditional-access-azure-portal.md)
#### [Komma igång](active-directory-conditional-access-azure-portal-get-started.md)
#### Snabbstarter
##### [Konfigurera MFA per app](active-directory-conditional-access-app-based-mfa.md)
##### [Kräv att användningsvillkoren godkänns](active-directory-conditional-access-tou.md)
##### [Blockera åtkomst när en sessionsrisk identifieras](active-directory-conditional-access-app-sign-in-risk.md)
#### Självstudier
##### [Migrera en klassisk MFA-princip](active-directory-conditional-access-migration-mfa.md)
#### Begrepp
##### [Baslinjeskydd](active-directory-conditional-access-baseline-protection.md)
##### [Villkor](active-directory-conditional-access-conditions.md)
##### [Platsvillkor](active-directory-conditional-access-locations.md)
##### [Kontroller](active-directory-conditional-access-controls.md)
##### [Konsekvensverktyg](active-directory-conditional-access-whatif.md)
##### [Förstå enhetsprinciper för Office 365-tjänster](active-directory-conditional-access-device-policies.md)
#### Instruktionsguider
##### [Bästa praxis](active-directory-conditional-access-best-practices.md)
##### [Konfigurera principer för villkorlig åtkomst för åtkomstförsök från ej betrodda nätverk](active-directory-conditional-access-untrusted-networks.md)
##### [Konfigurera enhetsbaserad villkorlig åtkomst](active-directory-conditional-access-policy-connected-applications.md)
##### [Konfigurera appbaserad villkorlig åtkomst](active-directory-conditional-access-mam.md)
##### [Ange användningsvillkor för användare och appar](active-directory-tou.md)
##### [Migrera klassiska principer](active-directory-conditional-access-migration.md)
##### [Konfigurera VPN-anslutning](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/always-on-vpn-deploy)
##### [Konfigurera SharePoint och Exchange Online](active-directory-conditional-access-no-modern-authentication.md)
##### [Reparation](active-directory-conditional-access-device-remediation.md)
#### [Teknisk referens](active-directory-conditional-access-technical-reference.md)
#### [Vanliga frågor och svar](active-directory-conditional-faqs.md)

### Certifikatbaserad autentisering
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
#### [Komma igång](active-directory-certificate-based-authentication-get-started.md)

### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [Aktivera](active-directory-identityprotection-enable.md)
#### [Hitta sårbarheter](active-directory-identityprotection-vulnerabilities.md)
#### [Riskhändelser](active-directory-identity-protection-risk-events.md)
#### [Meddelanden](active-directory-identityprotection-notifications.md)
#### [Inloggning](active-directory-identityprotection-flows.md)
#### [Simulera riskhändelser](active-directory-identityprotection-playbook.md)
#### [Avblockera användare](active-directory-identityprotection-unblock-howto.md)
#### [Vanliga frågor och svar](active-directory-identity-protection-faqs.md)
#### [Ordlista](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](active-directory-privileged-identity-management-configure.md)

## Integrera andra tjänster med Azure AD 
### [Integrera LinkedIn med Azure AD](linkedin-integration.md)

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
## [Tjänstens begränsningar](active-directory-service-limits-restrictions.md)

# Relaterat
## [Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Azure AD för utvecklare](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

# Resurser
## [Azures feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Azure-översikt](https://azure.microsoft.com/roadmap/?category=security-identity)
## [MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Prissättning](https://azure.microsoft.com/pricing/details/active-directory/)
## [Priskalkylator](https://azure.microsoft.com/pricing/calculator/)
## [Tjänstuppdateringar](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
