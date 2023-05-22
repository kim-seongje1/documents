# RFC 7030 - Enrollment over Secure Transport  

### 1. Introduction

<br/>

Architecturally, the EST service is located between a Certification Authority (CA) and a client. It performs several functions traditionally allocated to the Registration Authority (RA) role in a PKI. 

> 구조적으로, EST 서비스는 CA와 클라이언트 사이에 위치합니다. 기본적으로 PKI에서 RA의 역할에 할당된 여러 기능들을 수행합니다.

<br/>

EST adopts the Certificate Management Protocol (CMP) [RFC4210] model for CA certificate rollover, but it does not use the CMP message syntax or protocol. EST servers are extensible in that new functions may be defined to provide additional capabilities not specified in CMC [RFC5272], and this document defines two such extensions: one for requesting Certificate Signing Request attributes and another for requesting server-generated keys.

> EST는 CA 인증서 갱신을 위해 인증서 관리 프로토콜(CMP) 모델을 채택하지만, CMP 메시지 구문이나 프로토콜을 사용하지 않습니다. EST 서버는 CMC에 명세되어 있지 않은 추가적인 기능들을 정의할 수 있다는 점에서 확장 가능하며, 이 문서에서는 2가지 확장 기능을 정의합니다. 하나는 인증서 서명 요청(CSR) 속성을 요청하기 위한 것이고, 다른 하나는 서버에서 생성된 키들을 요청하기 위한 것입니다.

<br/>

EST specifies how to transfer messages securely via HTTP over TLS (HTTPS) [RFC2818], where the HTTP headers and media types are used in conjunction with TLS. HTTPS operates over TCP; this document does not specify EST over HTTP/Datagram Transport Layer Security/User Datagram Protocol (HTTP/DTLS/UDP). With a suitable specification for combining HTTP, DTLS, and UDP, there are no EST requirements that would prevent it from working over such a stack.

> EST는 HTTPS를 통해 메시지를 안전하게 전송하는 방법을 정의합니다. 여기서 TLS와 함께 HTTP 헤더 및 미디어 타입이 사용됩니다. HTTPS는 TCP 상에서 동작하며, 이 문서는 HTTP/DTLS/UDP 상의 EST를 정의하지 않습니다. 단, HTTP/DTLS/UDP 등과 결합을 위한 적절한 스펙을 이러한 스택에서 동작하지 못하게 하는 EST 요구사항은 없습니다.

<br/>

<br/>

![est_layering](./asset/est_layering.png)

</br>

### 1.1. Terminology

<br/>

**EST CA** : For certificate issuing services, the EST CA is reached through the EST server. The CA could be logically "behind" the EST server or embedded within it.

> 인증서 발급 서비스의 경우, EST CA는 EST 서버를 통해 연결됩니다. CA는 논리적으로 EST 서버 '뒤'에 있거나 그 안에 내장되어 있을 수 있습니다.

</br>

**Third-Party Trust Anchor** : Any trust anchor (TA) that is not authoritative for the PKI hierarchy for which the EST server is providing services.

> EST 서버가 서비스를 제공하는 PKI 계층에 대해 권한이 없는 모든 TA를 의미합니다.

</br>

**Explicit Trust Anchor** : Any TA that is explicitly configured on the client or server for use during EST TLS authentication. For example, a TA that is manually configured on the EST client or bootstrapped as described in Section [4.1.1].

> EST TLS 인증 중에 사용하기 위해 클라이언트 혹은 서버에 명시적으로 구성된 모든 TA를 의미합니다. 예를 들어, EST 클라이언트에서 수동으로 구성되거나, 4.1.1장에 설명된 대로 부트스트랩된 TA입니다.

</br>

**Implicit Trust Anchor** : Any third-party TA that is available on the client or server for use during TLS authentication but is not specifically indicated for use during EST TLS authentication. For example, TAs commonly used by web browsers to authenticate web servers or TAs used by servers to authenticate manufacturer-installed client credentials (such as certificates populated into cable modems or routers in the factory). The authorization model for these TAs is different from the authorization model for Explicit Trust Anchors.

> TLS 인증 중에 사용하기 위해 클라이언트 혹은 서버에서 유효한 third-party TA를 의미하지만, EST TLS 인증 중에 사용하도록 특별히 지정되지 않은 모든 third-party TA입니다. 예를 들어, 웹 서버를 인증하기 위해 웹브라우저에서 일반적으로 사용되는 TA 또는 제조 업체에 설치한 클라이언트 자격 증명(예 : 공장에서 케이블 모뎀 또는 라우터에 부여된 인증서)을 인증하기 위해 서버에서 사용하는 TA가 있습니다. 이러한 TA에 대한 권한 부여 모델은 Implicit Trust Anchor의 권한 부여 모델과 다릅니다.

</br>

**Certificate-Less TLS** : Certificate-less TLS cipher suites provide a way to perform mutual authentication in situations where neither the client nor server have certificates or are willing to use them. The credential used for authentication is a word, phrase, code, or key that is shared between the client and server. The credential must be uniquely shared between the client and server in order to provide authentication of an individual client to an individual server.

> 인증서가 없는 TLS 암호화 스위트는 클라이언트와 서버 모두 인증서가 없거나, 인증서를 사용하지 않는 상황에서 상호 인증을 수행하는 방법을 제공합니다. 인증에 사용되는 자격 증명은 클아이언트와 서버 간에 공유되는 단어, 구문, 코드, 또는 키와 같은 것입니다. 자격 증명은 개별 클라이언트의 인증을 각 서버에 제공하기 위해 클라이언트와 서버 간에 고유하게 공유되어야 합니다.