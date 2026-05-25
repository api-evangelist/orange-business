# Orange Business (orange-business)

Orange Business is the B2B, telco-cloud, and developer arm of Orange S.A. — France's leading telecommunications group operating across Europe, the Middle East, and Africa. It markets itself as "an operator, integrator, and platform player" and serves 30,000+ enterprise customers across 65 countries with cloud, cybersecurity, SD-WAN/SASE, 5G, IoT, data, AI, and digital-workplace services. The developer surface combines the Orange Open Gateway (Orange's implementation of GSMA Open Gateway / CAMARA standardised network APIs) with Orange's own portfolio of IoT, payments, communications, identity, cloud, and B2B-operations APIs.

**URL:** [Visit APIs.json](https://raw.githubusercontent.com/api-evangelist/orange-business/refs/heads/main/apis.yml)

**Run:** [Capabilities Using Naftiko](https://github.com/naftiko/fleet?utm_source=api-evangelist&utm_medium=readme&utm_campaign=company-api-evangelist&utm_content=repo)

## Tags

5G, AI, B2B, CAMARA, Cloud, Communications, Cybersecurity, Developer Platform, Digital Workplace, Enterprise, France, IoT, Identity, Mobile Money, Network APIs, Open Gateway, Orange, Payments, SD-WAN, SMS, SASE, Telco, Voice

## APIs

### Network APIs (Orange Open Gateway — CAMARA)

#### Orange Business Number Verification API
Silent, network-based mobile phone number verification using the SIM in the end user's device. Replaces SMS OTP and authenticator flows. Live in France and Spain through the Orange Open Gateway.

- [Documentation](https://docs.developer.orange.com/network-apis/api-catalog/number-verification/playground/current/overview)
- [OpenAPI](openapi/orange-business-number-verification-openapi.yml)
- [CAMARA Source](https://github.com/camaraproject/NumberVerification)

#### Orange Business SIM Swap API
Detect whether the SIM associated with a mobile phone number has been swapped recently. Used to flag account takeover risk before high-value actions.

- [Documentation](https://docs.developer.orange.com/network-apis/api-catalog/sim-swap/playground/current/overview)
- [OpenAPI](openapi/orange-business-sim-swap-openapi.yml)
- [CAMARA Source](https://github.com/camaraproject/SimSwap)

#### Orange Business Device Swap API
Detect changes to the device (IMEI) associated with a SIM. Surfaces risk signals beyond SIM swap.

- [Documentation](https://docs.developer.orange.com/network-apis/api-catalog/device-swap/es/current/overview)
- [OpenAPI](openapi/orange-business-device-swap-openapi.yml)
- [CAMARA Source](https://github.com/camaraproject/DeviceSwap)

#### Orange Business KYC Match API
Match user-provided KYC attributes (name, address, date of birth, document numbers) against the operator's authoritative subscriber record.

- [Documentation](https://docs.developer.orange.com/network-apis/api-catalog/kyc-match/playground/current/overview)
- [OpenAPI](openapi/orange-business-kyc-match-openapi.yml)
- [CAMARA Source](https://github.com/camaraproject/KnowYourCustomerMatch)

#### Orange Business Device Location Retrieval API
Retrieve the network-derived location of a mobile device with operator-provided accuracy and confidence.

- [Documentation](https://developer.orange.com/apis/device-location-retrieval-camara/)
- [OpenAPI](openapi/orange-business-device-location-retrieval-openapi.yml)
- [CAMARA Source](https://github.com/camaraproject/DeviceLocation)

#### Orange Business Device Location Verification API
Verify whether a mobile device is within a specified geographic area without exposing precise coordinates.

- [Documentation](https://developer.orange.com/apis/device-location-verification-camara/)
- [OpenAPI](openapi/orange-business-device-location-verification-openapi.yml)
- [CAMARA Source](https://github.com/camaraproject/DeviceLocation)

#### Orange Business Geofencing API
Create event subscriptions that notify when a device enters or leaves a defined geographic area.

- [Documentation](https://developer.orange.com/apis/camara-geofencing/)
- [OpenAPI](openapi/orange-business-geofencing-openapi.yml)
- [CAMARA Source](https://github.com/camaraproject/DeviceLocation)

#### Orange Business Device Reachability Status API
Query whether a device is currently reachable over the mobile network (connected data, connected SMS, or not connected).

- [Documentation](https://developer.orange.com/apis/camara-device-reachability-status/)
- [OpenAPI](openapi/orange-business-device-reachability-status-openapi.yml)
- [CAMARA Source](https://github.com/camaraproject/DeviceStatus)

#### Orange Business Device Roaming Status API
Query whether a device is currently roaming and, when permitted, in which country.

- [Documentation](https://developer.orange.com/apis/camara-device-roaming-status/)
- [OpenAPI](openapi/orange-business-device-roaming-status-openapi.yml)
- [CAMARA Source](https://github.com/camaraproject/DeviceStatus)

#### Orange Business Quality on Demand API
Request a temporary boosted network quality profile (latency, throughput, packet-loss target) for a specified device session and application flow. Unlocks 5G slicing-style guarantees.

- [Documentation](https://developer.orange.com/apis/camara-quality-of-service-on-demand/)
- [OpenAPI](openapi/orange-business-quality-on-demand-openapi.yml)
- [CAMARA Source](https://github.com/camaraproject/QualityOnDemand)

#### Orange Business Population Density Data API
Predictive population density estimations for France and Corsica derived from anonymised aggregated mobile network signals.

- [Documentation](https://developer.orange.com/products/population-density-data/)
- [OpenAPI](openapi/orange-business-population-density-data-openapi.yml)
- [CAMARA Source](https://github.com/camaraproject/PopulationDensityData)

### IoT

#### Orange Business Live Objects API
Orange's IoT platform (also branded Datavenue) for connecting, managing, and ingesting data from IoT devices over LoRa, NB-IoT, LTE-M, and cellular.

- [Documentation](https://developer.orange.com/apis/datavenue)
- [Portal](https://liveobjects.orange-business.com/)

#### Orange Business IoT Managed Global Connectivity API
Manage Orange Business cellular IoT SIM fleets worldwide — activation, suspension, usage, location, diagnostics — across 200+ countries.

- [Documentation](https://developer.orange.com/apis/iot-managed-global-connectivity)

### Payments

#### Orange Business Orange Money WebPay API
Accept Orange Money mobile-wallet payments on web and mobile checkouts across Mali, Cameroon, Cote d'Ivoire, Senegal, Madagascar, Botswana, Guinea Conakry, Guinea Bissau, Sierra Leone, DR Congo, and Central African Republic.

- [Documentation](https://developer.orange.com/apis/om-webpay)

#### Orange Business Pay With Orange Bill API
Direct-carrier-billing API that lets merchants charge purchases to a customer's Orange mobile invoice.

- [Documentation](https://developer.orange.com/apis/pay-with-orange-bill)

### Communications

#### Orange Business SMS Middle East and Africa API
A2P SMS delivery API for Orange Middle East and Africa footprint.

- [Documentation](https://developer.orange.com/apis/sms)

#### Orange Business Voice as a Service API
Programmable voice / VoIP API for outbound calls, IVR, and click-to-call.

- [Documentation](https://developer.orange.com/apis/voice-api)

#### Orange Business Contact Everyone API
Multichannel contact and notification API — SMS, voice, email, and fax broadcast.

- [Documentation](https://developer.orange.com/apis/contact-everyone)

#### Orange Business Business Talk API
Programmable management for Business Talk, Orange Business's enterprise SIP trunking and IP voice service.

- [Documentation](https://developer.orange.com/apis/businesstalk)

#### Orange Business Messaging Pro Cameroon API
Cameroon-specific A2P messaging platform for enterprise SMS, USSD, and rich messaging.

- [Documentation](https://developer.orange.com/apis/messagingpro-cameroon)

### Cloud and Network

#### Orange Business Cloud Avenue API
REST API for Cloud Avenue, Orange Business's France-sovereign VMware-based managed IaaS.

- [Documentation](https://developer.orange.com/apis/cloud-avenue)

#### Orange Business Evolution Platform IaaS API
Sandbox IaaS environment for testing applications on the Evolution Platform.

- [Documentation](https://developer.orange.com/apis/evolution-platform-iaas-sandbox)

#### Orange Business EVPL Online API
Order, manage, and monitor Ethernet Virtual Private Line connectivity services across Orange Business's global Ethernet backbone.

- [Documentation](https://developer.orange.com/apis/evpl-online/)

#### Orange Business EVPL Monitoring API
Real-time monitoring API for EVPL and adjacent Orange Business network services.

- [Documentation](https://developer.orange.com/apis/api-monitoring-live)

#### Orange Business Content Delivery Boost API
Manage CDN edge caching, purge, and acceleration policies for content delivered over Orange's networks.

- [Documentation](https://developer.orange.com/apis/content-delivery-boost)

### Orange Business Services (B2B Operations)

#### Orange Business Core Information API
Customer-facing inventory API — list contracts, sites, services, and product instances under a B2B account.

- [Documentation](https://developer.orange.com/apis/orange-business-services-core-information)

#### Orange Business Ordering API
Place, modify, and cancel orders against the Orange Business Services product catalogue. Aligned with TM Forum TMF622-style product ordering.

- [Documentation](https://developer.orange.com/apis/orange-business-services-ordering)

#### Orange Business Order Tracking API
Track the lifecycle and milestones of an Orange Business Services order.

- [Documentation](https://developer.orange.com/apis/order-tracking-documentation)

#### Orange Business Billing API
Programmatic access to Orange Business Services M2M invoices, charges, and itemised usage records.

- [Documentation](https://developer.orange.com/apis/orange-business-services-billing-m2m)

#### Orange Business Incident API
Open, update, and track support tickets. Aligned with TM Forum TMF621 Trouble Ticket conventions.

- [Documentation](https://developer.orange.com/apis/orange-business-services-incident)

#### Orange Business API Place
Marketplace surface for discovering Orange Business APIs.

- [Documentation](https://developer.orange.com/apis/api-place)

#### Orange Business Operator Eligibility (France) API
Check broadband and fibre eligibility at a French address for operator partners.

- [Documentation](https://developer.orange.com/apis/operator-eligibility-fr)

#### Orange Business RIP Operator Eligibility (France) API
Public-initiative network (RIP) fibre eligibility check for French regional fibre rollouts.

- [Documentation](https://developer.orange.com/apis/rip-operator-eligibility-fr)

### Identity

#### Orange Business Live Identity Verify API
Real-time identity verification combining ID-document capture, liveness detection, and biometric match.

- [Documentation](https://developer.orange.com/apis/live-identity-verify)

#### Orange Business Live Identity Captcha API
Behavioural and challenge-based human-verification (captcha) API.

- [Documentation](https://developer.orange.com/apis/live-identity-captcha)

## Common Properties

- [Orange Developer Portal](https://developer.orange.com/)
- [Orange Business](https://www.orange-business.com/en)
- [Orange Developer Docs](https://docs.developer.orange.com/)
- [Network APIs — Orange Open Gateway](https://developer.orange.com/products/network-apis/)
- [Orange Open Gateway Announcement](https://developer.orange.com/blog/orange-open-gateway-the-new-era-of-digital-services/)
- [Orange LiveNet — Network APIs Business Unit](https://developer.orange.com/blog/orange-livenet-a-new-business-unit-to-market-network-apis/)
- [GSMA Open Gateway — Orange](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma_orgs/orange-2/)
- [CAMARA Project (Linux Foundation)](https://github.com/camaraproject)
- [Orange-OpenSource GitHub](https://github.com/Orange-OpenSource)
- [Hurl — HTTP request runner (Rust)](https://github.com/Orange-OpenSource/hurl)
- [Orange Boosted Bootstrap](https://github.com/Orange-OpenSource/Orange-Boosted-Bootstrap)
- [OUDS iOS — Orange Unified Design System](https://github.com/Orange-OpenSource/ouds-ios)
- [OUDS Android — Orange Unified Design System](https://github.com/Orange-OpenSource/ouds-android)
- [OUDS Flutter — Orange Unified Design System](https://github.com/Orange-OpenSource/ouds-flutter)
- [towards5gs-helm — 5G core Helm charts](https://github.com/Orange-OpenSource/towards5gs-helm)
- [Orange Developer Blog](https://developer.orange.com/blog/)
- [Orange Business Blog](https://www.orange-business.com/en/blogs)
- [Orange 5G Lab](https://5glab.orange.com/en/)
- [Orange Group News](https://www.orange.com/en/news)
- [Orange Business LinkedIn](https://www.linkedin.com/company/orange-business/)
- [Orange LinkedIn](https://www.linkedin.com/company/orange/)
- [Orange Business on X](https://twitter.com/orangebusiness)
- [Legal Notice](https://www.orange-business.com/en/legal-notice)
- [Personal Data Protection](https://www.orange-business.com/en/personal-data-protection)
- [Contact Orange Developer](https://developer.orange.com/contact)
- [Talk to Sales](https://developer.orange.com/talk-to-sales/)
- [Register](https://developer.orange.com/register)
- [Orange Business Solutions](https://www.orange-business.com/en/our-solutions)
- [Live Intelligence — Generative AI](https://www.orange-business.com/en/products/live-intelligence)
- [Live Objects (IoT) Portal](https://liveobjects.orange-business.com/)
- [Cloud Avenue Portal](https://cloud.orange-business.com/)

## Maintainers

**Kin Lane** — info@apievangelist.com
