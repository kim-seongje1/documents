# RFC 7030 - Enrollment over Secure Transport

### 2. Operational Scenario Overviews

</br>

Both the EST clients and server are configured with information that provides the basis for mutual authentication and for authorization. The specific initialization data depends on the methods available in the client and server, but it can include shared secrets, network service names and locations (e.g., a Uniform Resource Identifier (URI) [RFC3986]), trust anchor information (e.g., a CA certificate or a hash of a TA's certificate), and enrollment keys and certificates. Depending on an enterprise's acquisition and network management practices, some initialization may be performed by the vendor prior to delivery of client hardware and software. In that case, the client vendor may provide data, such as trust anchors, to the enterprise via a secure procedure. The distribution of this initial information is out of scope.

> EST 클라이언트와 서버는 모두 상호 인증 및 권한 부여에 대한 기반을 제공하는 정보로 구성됩니다. 특정 초기화 데이터는 클라이언트 및 서버에서 사용하는 방법에 따라 다르지만 공유 secrets, 네트워크 서비스 이름 및 위치(예: URI), TA 정보(예: CA 인증서 또는 TA 인증서 해시), 등록 키 및 인증서 등을 포함합니다. 기업의 이익 및 네트워크 관리 방식에 따라 클라이언트 하드웨어와 소프트웨어를 제공하기 전에 공급 업체에서 일부 초기화를 수행할 수 있습니다. 이 경우, 클라이언트 공급 업체는 안전한 절차를 통해 TA와 같은 데이터를 제공할 수 있습니다. 단, 이 초기화 정보의 배포는 범위 밖입니다.

</br>

Distribution of trust anchors and other certificates can be effected via the EST server. However, nothing can be inferred about the authenticity of this data until an out-of-band mechanism is used to verify them.

> TA 및 다른 인증서에 대한 배포는 EST 서버를 통해 수행할 수 있습니다. 그러나 대역 외 메커니즘을 사용하여 데이터를 검증하기 전까지는 해당 데이터의 진위 여부를 알 수 없습니다.

</br>

The general client/server interaction proceeds as follows :

> 일반적인 클라이언트/서버 간 상호 작용은 다음과 같이 진행됩니다.

</br>

	1. The client initiates a TLS-secured HTTP session with an EST server.
	
	- 클라이언트는 EST 서버와 TLS 보안 HTTP 세션을 초기화합니다.
	
	2. A specific EST service is requested based on a portion of 
	   the URI used for the session.
	   
	- 해당 세션에 사용되는 URI의 일부를 기반으로 특정 EST 서비스가 요청됩니다.
	
	3. The client and server authenticate each other.
	
	- 클라이언트와 서버가 서로를 인증합니다.
	
	4. The client verifies that the server is authorized to serve this client.
	
	- 클라이언트는 해당 서버가 클라이언트를 서비스할 권한이 있는지 확인합니다.
	
	5. The server verifies that the client is authorized to make use of
	   this server and the request that the client has made.
	
	- 서버는 해당 클라이언트가 서버 및 클라이언트 요청 사용 권한이 있는지 확인합니다.
	
	6. The server acts upon the client request.
	 
	- 서버는 클라이언트의 요청에 따라 동작합니다.

</br>

### 2.1. Obtaining CA Certificates

</br>

The EST client can request a copy of the current EST CA certificate(s) from the EST server. The EST client is assumed to perform this operation before performing other operations.

> EST 클라이언트는 EST 서버로부터 현재 EST CA 인증서의 사본을 요청할 수 있습니다. EST 클라이언트는 다른 작업들을 수행하기 전에 이 작업을 수행한다고 가정합니다.

</br>

Throughout this document we assume the EST CA has a certificate that is used by the client to verify signed objects issued by the CA, e.g., certificates and certificate revocation lists (CRLs), and that a different certificate than the one used to verify signatures on certificates and CRLs is used when EST protocol communication requires additional encryption.

> 이 문서 전체에서 EST CA는 클라이언트가 CA에서 발행한 서명된 개체(예: 인증서 및 인증서 해지 목록(CRLs))를 검증하는 데 사용되는 인증서를 가지고 있다고 가정합니다. 또한 EST 프로토콜 통신에 추가적인 암호화가 필요한 경우, 인증서 및 CRL의 서명을 확인하는 데 사용되는 인증서와는 다른 인증서가 사용됩니다.

</br>

The EST client authenticates and verifies the authorization scope of the EST server when requesting the current CA certificate(s). As detailed in Sections [3.3.1] and [3.3.3], available options include :

> EST 클라이언트는 현재 CA 인증서를 요청할 때, EST 서버의 인증 범위를 인증하고 확인합니다. 섹션 [3.3.1]과 [3.3.3]에 자세히 설명된 대로, 사용 가능한 옵션을 다음과 같습니다.

</br>

	* Verifying the EST server's HTTPS URI against the EST server's certificate 
	  using Implicit TAs (similar to a common HTTPS exchange). This allows the 
	  EST server and client to leverage existing TAs that might be known to the
	  EST client.
	
	- Implicit TA를 사용하여 EST 서버 인증서와 대응된 EST 서버의 HTTPS URI를 검증(일반적인
	  HTTPS 교환과 유사)합니다. 이를 통해 EST 서버/클라이언트는 EST 클라이언트가 알 수 있는 기존
	  TA를 활용할 수 있습니다.
	
	* The client can leverage a previously distributed trust anchor specific to 
	  the EST server. This allows the EST client to use an existing, potentially 
	  older, CA certificate to request a current CA certificate.
	
	- 클라이언트는 EST 서버에 이전에 분산된 특정 TA를 활용할 수 있습니다. 이를 통해 EST 클라이언트는
	  기존의, 잠재적으로 오래된 CA 인증서를 사용하여 현재 CA 인증서를 요청할 수 있습니다.
	
	* For bootstrapping, the EST client can rely upon manual authentication 
	  performed by the end-user as detailed in Section [4.1.1]
	
	- 부트스트랩의 경우, EST 클라이언트는 섹션 [4.1.1]에서 자세히 설명된 대로 최종 사용자에 의해 
	  수행된 인증 매뉴얼에 의존할 수 있습니다.
	
	* The client can leverage the binding of a shared credential to a specific EST 
	  server with a certificate-less TLS cipher suite.
	
	- 클라이언트는 인증서가 없는 TLS 암호화 스위트를 통해 특정 EST 서버에 공유 자격 증명을 바인딩할 수
	  있습니다.

</br>

### 2.2. Initial Enrollment

</br>

After authenticating an EST server and verifying that it is authorized to provide services to the client, an EST client can acquire a certificate for itself by submitting an enrollment request to that server.

> EST 서버를 인증하고 클라이언트에 서비스를 제공할 수 있는 권한이 있는지 확인한 후, EST 클라이언트는 해당 서버에 등록 요청을 제출함으로써 자신의 인증서를 얻을 수 있습니다.

</br>

The EST server authenticates and authorizes the EST client as specified in Sections [3.3.2], [3.3.3], and [3.7]. The methods described in the normative text that are discussed in this overview include :

> EST 서버는 섹션 [3.3.2], [3.3.3], [3.7]에 명세된 대로 EST 클라이언트를 인증하고, 권한을 부여합니다. 이 개요에서 설명하는 방법들은 아래와 같습니다.

</br>

- **TLS with a previously issued client certificate**
	(e.g., an existing certificate issued by the EST CA)

	`* 이전에 발급된 클라이언트 인증서가 있는 TLS (예: EST CA에서 발급한 기존 인증서)`

- **TLS with a previously installed certificate** 
	(e.g., manufacturer-installed certificate or a certificate issued by some other party)

	`* 이전에 설치된 인증서가 있는 TLS (예: 제조 업체가 설치한 인증서 또는 다른 당사자가 발행한 인증서)`

- **Certificate-less TLS** 
	(e.g., with a shared credential distributed out-of-band)
	
	`* 인증서가 없는 TLS (예: 대역외 분산된 공유 자격 증명을 포함)`

- **HTTP-based with a username/password distributed out-of-band**

	`* 대역외 분산된 username/password를 포함한 HTTP 기반`

</br>

#### 2.2.1. Certificate TLS Authentication

</br>

If the EST client has a previously installed certificate issued by a third-party CA, this certificate can be used to authenticate the client's request for a certificate from the EST server (if that CA is recognized by the EST server). An EST client responds to the EST server's TLS certificate request message with the existing certificate already held by the client. The EST server will verify the client's existing certificate and authorize the client's request as described in Section [3.3.2].

> EST 클라이언트가 이전에 설치된 타사 CA 인증서를 갖고 있다면, 해당 인증서를 사용하여 EST 서버 인증서에 대한 클라이언트 요청을 인증할 수 있습니다(해당 CA가 EST 서버에 알려진 경우). EST 클라이언트는 클라이언트가 이미 보유하고 있는 기존 인증서로 EST 서버의 TLS 인증서 요청에 대한 응답을 수행합니다. EST 서버는 클라이언트의 기존 인증서를 확인하고, 섹션 [3.3.2]에 설명된 대로 클라이언트 요청을 승인합니다.

</br>

#### 2.2.2. Certificate-Less TLS Authentication

</br>

The EST client and EST server can be mutually authenticated using a certificate-less TLS cipher suite (see Section [3.3.3]).

> EST 클라이언트 및 EST 서버는 인증서가 없는 TLS 암호화 스위트를 사용하여 상호 인증될 수 있습니다.

</br>

#### 2.2.3. HTTP-Based Client Authentication

</br>

The EST server can optionally also request that the EST client submit a username/password using the HTTP Basic or Digest authentication methods (see Section [3.2.3]). This approach is desirable if the EST client cannot be authenticated during the TLS handshake (see Section [3.3.2]) or the EST server policy requires additional authentication information (see Section [3.2.3]). In all cases, HTTP-based client authentication is only to be performed over a TLS-protected transport (see Section [3.3]).

> EST 서버는 선택적으로 EST 클라이언트가 HTTP Basic 또는 Digest 인증 방법을 사용하여 username/password를 제출하도록 요청할 수도 있습니다. 이러한 접근은 EST 클라이언트가 TLS 핸드셰이크 중에 인증될 수 없거나 EST 서버 정책에 추가 인증 정보가 필요한 경우에 바람직합니다. 모든 경우에, HTTP 기반 클라이언트 인증은 TLS로 보호된 전송을 통해서만 수행됩니다.

</br>

### 2.3. Client Certificate Reissuance

</br>

An EST client can renew/rekey its existing client certificate by submitting a re-enrollment request to an EST server.

> EST 클라이언트는 EST 서버에 재등록 요청을 제출하여 기존 클라이언트 인증서를 갱신할 수 있습니다.

</br>

When the current EST client certificate can be used for TLS client authentication (see Section [3.3.2]), the client presents this certificate to the EST server for client authentication. When the to be reissued EST client certificate cannot be used for TLS client authentication, any of the authentication methods used for initial enrollment can be used.

> 현재 EST 클라이언트 인증서를 TLS 클라이언트 인증에 사용할 수 있는 경우, 클라이언트는 인증을 위해 이 인증서를 EST 서버에 제공합니다. 재발급되어야 할 EST 클라이언트 인증서를 TLS 클라이언트 인증세 사용할 수 없는 경우, 최초 등록에 사용된 모든 인증 방법들을 사용할 수 있습니다.

</br>

For example, if the client has an alternative certificate issued by the EST CA that can be used for TLS client authentication, then it can be used.

> 예를 들어, 클라이언트에 TLS 클라이언트 인증에 사용할 수 있는 EST CA에서 발급한 대체 인증서가 있다면, 이를 사용할 수 있습니다.

</br>

The certificate request message includes the same Subject and SubjectAltName as the current certificate. Name changes are requested as specified in Section [4.2.2].

> 인증서 요청 메시지는 현재 인증서와 동일한 Subject 및 SubjectAltName을 포함합니다. 이름 변경은 섹션 [4.2.2]에 명시된 대로 요청됩니다. 

</br>

### 2.4. Server Key Generation

</br>

The EST client can request a server-generated certificate and key pair (see Section [4.4]).

> EST 클라이언트는 서버에서 생성한 인증서 및 키 쌍을 요청할 수 있습니다.

</br>

### 2.5. Full PKI Request Messages

</br>

Full PKI Request [RFC5272] messages can be transported via EST using the Full CMC Request function. This affords access to functions not provided by the Simple Enrollment functions. Full PKI Request messages are defined in Sections [3.2] and [4.2] of [RFC5272]. See Section [4.3] for a discussion of how EST provides a transport for these messages.

> Full PKI Request 메시지는 Full CMC Request 기능을 사용하여 EST를 통해 전송할 수 있습니다. 이를 통해 Simple Enrollment 기능에서 제공하지 않는 다른 기능들에 엑세스할 수 있습니다. Full PKI Request 메시지는 [RFC5272]의 섹션 [3.2]와 [4.2]에 정의되어 있습니다. EST가 이러한 메시지에 대한 전송을 할 수 있는 방법은 섹션 [4.3]을 참조하십시오.

</br>

### 2.5. Certificate Signing Request (CSR) Attributes Request

</br>

Prior to sending an enrollment request to an EST server, an EST client can query the EST server for a set of additional attributes that the client is requested to use in a subsequent enrollment request.

> EST 서버에 등록 요청을 보내기 이전에, EST 클라이언트는 이후 등록 요청에 사용하도록 요청되는 추가적인 속성 집합을 EST 서버에게 질의할 수 있습니다.

</br>

These attributes can provide additional descriptive information that the EST server cannot access itself, such as the Media Access Control (MAC) address of an interface of the EST client. Alternatively, these attributes can indicate the kind of enrollment request, such as a specific elliptic curve or a specific hash function that the client is expected to use when generating the CSR.

> 이러한 속성들은 EST 클라이언트 인터페이스의 MAC 주소와 같이, EST 서버가 액세스할 수 없는 추가적인 설명 정보를 제공할 수 있습니다. 또는, 이러한 속성들은 클라이언트가 CSR을 생성할 때 사용하길 기대하는 특정 타원 곡선 또는 특정 해시 함수 등과 같은 등록 요청의 종류를 나타낼 수도 있습니다.