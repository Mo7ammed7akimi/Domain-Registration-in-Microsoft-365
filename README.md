# Domain Registration in Microsoft 365

A step-by-step guide to registering a new domain, or connecting an existing one, in the Microsoft 365 admin centre.

## Contents

- [Prerequisites](#prerequisites)
- [Option A: Register a new domain](#option-a-register-a-new-domain)
- [Option B: Add a domain registered elsewhere](#option-b-add-a-domain-registered-elsewhere)
- [DNS records reference](#dns-records-reference)
- [Set the domain as default](#set-the-domain-as-default)
- [Verify the setup](#verify-the-setup)
- [Troubleshooting](#troubleshooting)

## Prerequisites

- A Microsoft 365 subscription with an active tenant.
- An account with the **Global Administrator** or **Domain Name Administrator** role.
- A valid payment method (Option A only).
- Access to the DNS settings of the current registrar (Option B only).

> **Note:** Domain purchase inside Microsoft 365 is handled through a partner registrar and is not available in every region or tenant type. If the **Buy domain** button is missing, use Option B.

## Option A: Register a new domain

1. Sign in to the [Microsoft 365 admin centre](https://admin.microsoft.com).
2. Go to **Settings** > **Domains**.
3. Select **Buy domain**.
4. Enter the desired domain name and select **Search**.
5. Choose an available domain from the results and select **Buy**.
6. Review the price and renewal terms, then complete the checkout.
7. Wait for the confirmation message. The domain appears in the **Domains** list.
8. Open the domain and confirm that Exchange (email) and other required services are enabled. DNS records are normally configured automatically for purchased domains.

## Option B: Add a domain registered elsewhere

1. Sign in to the [Microsoft 365 admin centre](https://admin.microsoft.com).
2. Go to **Settings** > **Domains** and select **Add domain**.
3. Enter the domain name (for example, `example.com`) and select **Use this domain**.
4. Choose **Add a TXT record** as the verification method.
5. Copy the TXT record shown by Microsoft 365 and add it at your registrar's DNS settings:

   | Type | Host / Name | Value | TTL |
   |------|-------------|-------|-----|
   | TXT  | `@`         | `MS=msXXXXXXXX` (value shown in the wizard) | 3600 |

6. Wait a few minutes, then select **Verify** in the wizard. DNS propagation can take up to 48 hours.
7. Choose how to connect the domain:
   - **Add DNS records for me** (if the domain uses Microsoft-managed or a supported registrar), or
   - **I'll add the DNS records myself**.
8. Select the services to use (Exchange, Teams, and so on) and add the records listed in the wizard.
9. Select **Done** and check that the domain status reads **Healthy**.

## DNS records reference

The exact values are displayed in the admin centre. The records below are typical for Exchange Online.

| Type  | Host / Name | Value | Priority |
|-------|-------------|-------|----------|
| MX    | `@` | `<domain-with-dashes>.mail.protection.outlook.com` | 0 |
| CNAME | `autodiscover` | `autodiscover.outlook.com` | - |
| TXT (SPF) | `@` | `v=spf1 include:spf.protection.outlook.com -all` | - |
| CNAME (DKIM) | `selector1._domainkey` | `selector1-<domain-with-dashes>._domainkey.<tenant>.onmicrosoft.com` | - |
| CNAME (DKIM) | `selector2._domainkey` | `selector2-<domain-with-dashes>._domainkey.<tenant>.onmicrosoft.com` | - |
| TXT (DMARC) | `_dmarc` | `v=DMARC1; p=none; rua=mailto:dmarc@<domain>` | - |

Replace `<domain-with-dashes>` with the domain using dashes instead of dots (for example, `example-com`), and `<tenant>` with the tenant name.

## Set the domain as default

1. In **Settings** > **Domains**, select the domain.
2. Select **Set as default**.
3. New users will now receive addresses on this domain.

To change existing users, go to **Users** > **Active users**, select a user, then **Manage username and email**.

## Verify the setup

1. In **Settings** > **Domains**, confirm the status is **Healthy**.
2. Send a test email to and from a mailbox on the new domain.
3. Check DNS records from a terminal (PowerShell or Command Prompt):

   ```bash
   nslookup -type=MX example.com
   nslookup -type=TXT example.com
   ```

4. Enable DKIM signing under the Microsoft Defender portal: **Email & collaboration** > **Policies & rules** > **Threat policies** > **Email authentication settings** > **DKIM**.

## Troubleshooting

| Problem | Likely cause | Fix |
|---------|--------------|-----|
| Verification fails | DNS change not yet propagated | Wait and retry; confirm the TXT value matches exactly |
| Email not received | Missing or incorrect MX record | Check that the MX priority is 0 and old MX records are removed |
| Emails land in spam | SPF, DKIM or DMARC missing | Add all three records and enable DKIM signing |
| Domain shows errors | Required records missing | Open the domain and select **Fix issues** |
| **Buy domain** unavailable | Region or tenant restriction | Register elsewhere and follow Option B |

## References

- [Add a domain to Microsoft 365](https://learn.microsoft.com/microsoft-365/admin/setup/add-domain)
- [Buy a domain in Microsoft 365](https://learn.microsoft.com/microsoft-365/admin/get-help-with-domains/buy-a-domain-name)
- [Set up SPF, DKIM and DMARC](https://learn.microsoft.com/defender-office-365/email-authentication-about)
