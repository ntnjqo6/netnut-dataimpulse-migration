# netnut review: what the FBI seizure means for your scrapers, and how to move to a $1/GB consent-based pool

If you searched for a NetNut review, there's a decent chance you're not shopping — you're troubleshooting. Your residential proxy calls started failing, or you went looking for the pricing page and got something that doesn't look like a pricing page. Here's the short version before the long one: **on July 2, 2026, the FBI and Google disrupted NetNut's residential proxy network and seized its domains.** Your credentials may still resolve to something, but the service you paid for no longer exists in the form you bought it.

That single fact reshapes what a NetNut review should even contain. So this covers three things: what NetNut actually sold and charged, what independent testing said about it before the takedown, and how to get your scraping back online without a rewrite.

## What NetNut actually sold

NetNut sat in the enterprise-ish middle of the proxy market. It wasn't a budget option and it wasn't Bright Data. It sold four proxy types, all on monthly minimum commitments rather than true pay-as-you-go, according to Yahoo Tech's hands-on review:

**Rotating residential.** Advertised at more than 85 million IPs across 200+ countries — the largest residential pool among providers aimed at small businesses and individual buyers, per that review. Entry plan: $99/month for up to 28 GB. Top plan: $3,750/month for up to 2 TB. That's roughly $3.53/GB at the bottom and $1.88/GB at the top.

**Static residential (ISP proxies).** IPs sourced directly from ISPs, so you get residential authenticity with datacenter-style stability. Under a million to a few million IPs depending on which page you read, concentrated in the US and Europe. Entry plan: $99/month for 7 GB. Ceiling: $4,500/month for 1 TB. This was the expensive line — about $14/GB at entry.

**Datacenter.** 150,000+ private and shared IPs. Starter at $100/month for 100 GB, Master at $1,000/month for 2 TB. Cheapest product in the lineup.

**Mobile.** 3G/4G/5G and LTE IPs across 100+ countries, with country, state and city targeting. Starter at $99/month for 13 GB.

Annual billing was available on all four and ran a little cheaper than monthly. There was a seven-day free trial, which was genuinely useful. There was no pay-as-you-go option on mobile, and NetNut's FAQ stated it doesn't offer refunds, though it would consider a refund request "according to the plan's usage."

### The billing model was the real constraint

This is the part that mattered more than any single feature. NetNut sold subscriptions with monthly minimums. If your workload was spiky — 80 GB one month, 12 GB the next — you paid for the floor either way, and there's no indication unused traffic rolled over.

### Volume pricing told a different story

Proxyway's 2025 market research pricing table shows how steep those volume discounts got: about $1.00/GB at 50 GB per month, $0.74 at 100 GB, $0.70 at 250 GB, $0.50 at 500 GB, and $0.46 at 1 TB. So the "$99 for 28 GB" headline was just the entry point. At scale, NetNut's effective rate got competitive with far cheaper providers.

Keep that number in mind. It decides whether switching saves you anything or just moves your problem.

## What testing and users said before the takedown

NetNut generally performed well in independent testing. Proxyway's 2025 research put it in the top group for success rate, with a median of 99.78% and a best-in-class 100%. It also streamed 4K comfortably during that round of testing. Reviews on Software Advice from users in IT services, staffing and healthcare described reliable connections, clean IPs, working rotation and useful geo-targeting for localized SEO checks and ad verification.

Two consistent criticisms showed up across sources. First, price — Yahoo Tech noted that NetNut's few negative Trustpilot reviews clustered around it being more expensive than competitors, where it held a 4.7/5 score at the time. Second, support consistency: Software Advice reviewers mentioned live chat that wasn't always instantly responsive, and a dashboard they wanted to be more intuitive.

Proxyway also observed something worth flagging for anyone running difficult targets: in their testing, NetNut's proxies came from a single subnet. They noted those subnets were "better rested," which is why they held up under load. Resting subnets and IP diversity are different things, and if your use case needs the latter, that's a limitation regardless of who owns the network.

## July 2, 2026: what changed

Malwarebytes summarized it bluntly: "NetNut is a malicious service built on millions of hijacked consumer devices."

In a joint operation, Google's Threat Intelligence Group disabled the Google accounts and services NetNut used for command and control, while the FBI and IRS Criminal Investigation seized hundreds of domains tied to the network. The underlying infrastructure was tracked by security researchers as the **Popa** botnet, reportedly running on more than two million compromised devices — smart TVs, streaming boxes, Android phones — whose owners had not agreed to share anything. Enrollment typically happened through "bandwidth sharing" apps that promised payouts for unused internet.

The documented uses of the network included password spraying — cycling stolen or guessed credentials against a target — along with content scraping, ad fraud and account takeover.

Two practical details:

- **netnut.com now displays an FBI seizure banner**, with nameservers pointing at `ns1.fbi.seized.gov`.
- **netnut.io was still reachable** when ProxyStats checked on July 6, 2026. A working storefront and a seized domain can coexist. Neither means the network behind it is intact.

So there are two separate problems if you were a customer. The obvious one: your proxy infrastructure was seized, so reliability is gone. The less obvious one: the IPs you routed client work through came from devices whose owners never consented. If you ran compliance-sensitive work — ad verification for a brand, price monitoring feeding a client report, anything an auditor might ask about — you inherited a supply-chain problem you didn't choose.

## If you're still running NetNut credentials

1. Stop routing production traffic through the service today. Failures and partial success are worse than clean failure when you're collecting data.
2. Export whatever billing and usage records you can still reach. Domains are seized and support appearances suggest you shouldn't count on a refund — if you paid by card recently, talk to your bank about your options.
3. Grep your codebase and CI secrets for the old gateway host, username and any hardcoded ports. Environment variables, scraping framework configs, scheduler secrets and proxy settings panels are the usual hiding spots.
4. Pick a replacement and swap credentials. This is a configuration change, not a rewrite — you're updating a host, port, username and password.

## Why DataImpulse is the cleanest drop-in

DataImpulse runs its own pool of more than 90 million residential, mobile and datacenter IPs across 195 locations. The sourcing model is the inverse of the Popa story: IPs come from people who opt in through a disclosed SDK and get paid for the bandwidth they share, and the pool is first-party rather than resold.

The billing model is what most ex-NetNut users notice first. It's pay-as-you-go from $1/GB with a subscription-free account, and bought traffic doesn't expire. For a workload that was fighting a monthly minimum, that's the difference between paying for capacity and paying for results.

The mechanical part maps almost one-to-one, which matters if you have a deadline:

| What you used | Where it goes |
| --- | --- |
| NetNut residential gateway | `gw.dataimpulse.com` |
| Port | `823` |
| Auth | username and password |
| Rotation | per request, or sticky sessions up to 120 minutes |
| Targeting | country included; city, state, ASN, ZIP as paid add-ons |
| Protocols | HTTP, HTTPS, SOCKS5 |
| Billing | pay-as-you-go from $1/GB, traffic never expires |

DataImpulse publishes a 99.51% success rate, supports sub-users and an API, and lists 24/7 human support — which is the part people miss when the migration hits a snag at 2 a.m. TechRadar's review of the service reported consistently high scraping success rates in its tests, and ProxyStats' live benchmark ranked it first among NetNut replacements as of July 6, 2026, with a 514 ms median latency and a 100% clean-IP rate.

👉 [Check the current DataImpulse pricing and start with a $5 test balance](https://bit.ly/dataimPulse)

## All four DataImpulse product lines and what they cost

Pricing below reflects the published per-GB rates, including the volume tier that kicks in at 1 TB and above.

| Proxy type | What's included | Standard rate | 1 TB+ rate | Billing | Get started |
| --- | --- | --- | --- | --- | --- |
| Residential | 90M+ rotating and sticky IPs, 195 locations, country targeting included, HTTP/HTTPS/SOCKS5 | $1/GB (intro: $5 for 5 GB) | $0.80/GB | Pay-as-you-go, traffic never expires | [Start with 5 GB](https://bit.ly/dataimPulse) |
| Datacenter | Randomised datacenter subnets, 99.9% uptime | $0.50/GB (intro: $5 for 10 GB) | $0.45/GB | Pay-as-you-go, traffic never expires | [Start with 10 GB](https://bit.ly/dataimPulse) |
| Mobile | Real 4G/5G/LTE mobile IPs for harder targets | $2/GB (intro: $5 for 2.5 GB) | $1.60/GB | Pay-as-you-go, traffic never expires | [Start with mobile traffic](https://bit.ly/dataimPulse) |
| Premium residential | Faster, higher-trust residential traffic, dedicated account manager, all targeting options included at no surcharge | $5/GB (intro: $5 for 1 GB), $50 for 10 GB | Custom pricing from 5 TB | Pay-as-you-go, traffic never expires | [Start with premium residential](https://bit.ly/dataimPulse) |

Three notes that belong next to that table rather than buried in it.

Country-level targeting is included in the base rate on all lines. City, state, ASN and ZIP targeting is a paid add-on — one review site reports advanced filters billed at roughly double the standard per-GB rate on standard residential plans, while DataImpulse's own documentation calls it a small extra fee. If your workload is ZIP-heavy, get that confirmed in writing before you budget.

AIMultiple's breakdown notes a seven-day refund policy for new users. Worth checking at checkout rather than assuming.

And the honest math: at 1 TB per month, NetNut's old effective rate was around $0.46/GB while DataImpulse sits at $0.80/GB. **If you were a genuine 1 TB-per-month customer on a stable monthly pattern, price is not your reason to move.** The savings land at irregular and small-to-mid volumes, where subscriptions punish you and pay-as-you-go doesn't.

## Where DataImpulse isn't the right answer

It's worth saying plainly, because it'll save you a support ticket.

There's **no static ISP product**. DataImpulse's four lines are residential, datacenter, mobile and premium residential. If your NetNut workload was built on static residential IPs for long-lived social accounts or persistent logins, you need to solve that elsewhere or rebuild around sticky sessions capped at 120 minutes.

There's **no per-IP monthly rental model** either. If you were buying specific dedicated IPs on a per-address basis, this is a per-GB world.

And if you need **UDP traffic**, DataImpulse's protocol support is HTTP, HTTPS and SOCKS5, with UDP handled on request rather than as a standard option.

👉 [Compare what $1/GB actually covers on the DataImpulse site](https://bit.ly/dataimPulse)

## Migrating without rewriting your scraper

The reason this migration is mostly boring is that residential proxies all authenticate the same way across the industry. You're changing values, not logic.

Grab the new credentials from the dashboard — proxy login, password, gateway host and port. If you were rotating sessions or managing sub-users through NetNut's API, DataImpulse exposes the same controls through its dashboard and API, so those calls need new endpoints rather than new architecture.

Then decide your session behaviour. Rotating sessions give you a fresh IP per request, which suits high-volume crawling and spreads repeated hits across the pool. Sticky sessions hold one IP for up to 120 minutes, which is better when a target performs differently mid-session — a login flow, a multi-step checkout path, a paginated dataset that cares about continuity.

Finally, test small against a target you know well before you cut over. Check that the exit country matches what you asked for and that your success rate holds. Because the traffic doesn't expire, a 5 GB test run costs you five dollars of balance you still have afterward, rather than eating a monthly allowance you already paid for.

## FAQ

**Is NetNut still working?** Its residential network was disrupted on July 2, 2026 and its domains were seized. Some infrastructure may still respond to credentials, but you're routing production data through a seized network. Don't.

**Can I get my unused NetNut balance back?** Realistically, no — the domains are seized and support is reportedly unreachable. Recent card payments are a bank conversation, not a vendor one.

**Do I have to rewrite my scraper?** No. Host, port, username and password. If you built against NetNut's API for session control or sub-users, you'll update those calls to DataImpulse's API endpoints.

**What does $1/GB actually buy?** Residential IPs from 195 locations with country targeting included, HTTP/HTTPS/SOCKS5, and rotating or sticky sessions. Advanced geo filters cost extra.

**Is a $1/GB pool automatically sketchy?** This is the right question to ask after the Popa takedown, and the answer is that price alone doesn't tell you. Ask any provider where the IPs come from, whether device owners consented, and what they're paid. DataImpulse's answer is a disclosed opt-in SDK on a first-party pool — and their $1/GB is a volume-and-efficiency claim, not a sourcing shortcut. Get the answer in writing before you route client work through anyone.

## The bottom line

NetNut wasn't a bad service while it ran. It had a large pool, decent independent test results, and users who liked its geo-targeting. It was also expensive at entry, subscription-bound, and sitting on top of a network built from devices whose owners never agreed to anything — which is the part that turned from an abstract sourcing question into a compliance problem on July 2, 2026.

If you're leaving it, the practical version of this review is short: export your records, grep for the old endpoint, and move your credentials somewhere with a supply chain you can explain to someone who asks. DataImpulse maps onto the old setup with a host, a port and a password change — and at small or spiky volumes, it's also cheaper.

👉 [Move your workload over and keep your test traffic non-expiring](https://bit.ly/dataimPulse)
