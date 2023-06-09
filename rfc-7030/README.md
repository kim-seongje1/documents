# RFC 7030 - Enrollment over Secure Transport

This document profiles certificate enrollment for clients using **Certificate Management over CMS** (CMC) messages over a secure transport. This profile, called **Enrollment over Secure Transport** (EST), describes a simple, yet functional, certificate management protocol targeting **Public Key Infrastructure** (PKI) clients that need to acquire client certificates and associated Certification Authority (CA) certificates. It also supports client-generated public/private key pairs as well as key pairs generated by the CA.

> 이 문서는 보안 전송 간 CMC(Certificate Management over CMS) 메시지를 사용하여 클라이언트에 대한 인증서 등록을 설명합니다. EST(Enrollment over Secure Transport)는 클라이언트 인증서 및 연관된 CA 인증서를 획득해야 하는 PKI(Public Key Infrastructure) 클라이언트를 대상으로 하는 인증서 관리 프로토콜을 설명합니다. 또한 클라이언트로부터 생성된 공개키/개인키 쌍 및 CA에서 생성한 키 쌍도 지원합니다.

</br>

## Table of Contents

1.  **Introduction**
    
2.  **Operational Scenario Overviews**
    
3.  **Protocol Design and Layer**
    
4.  **Protocol Exchange Details**
    
5.  **IANA Considerations**
    
6.  **Security Considerations**
    
7.  Appendix : **Operational Scenario Example Message**