# TWI Requirements

**NOTE: This document is being moved to GitHub. The link to the document will be posted to the TWI mailing list. Please refrain from editing at this time.**

The evolving regulatory environment coupled with a steady stream of high-profile and costly breaches is leading enterprises and regulators alike to focus on securing and governing deployed workloads. An increasingly pressing need in enterprise IT is being able to securely authenticate workloads to each other and controlling which workloads can access what data. This need has been recognized and a variety of approaches are competing for adoption and viability, such as the [SPIFFE/SPIRE architecture](https://spiffe.io/docs/latest/architecture/), the [WIMSE working group at the IETF](https://www.ietf.org/blog/wimse-working-group/) and others.

While it is quite clear that for the foreseeable future this need is not going to be met by Confidential Computing alone, the security features related to workload identification and isolation offered by Confidential Computing eclipse other approaches. Confidential Computing will not be first to market, and thus will need to evolve to fit within, and ideally serve as the North Star of workload identity as it becomes a foundational pillar of trustworthy and governable enterprise computing. Remote Attestation plays a key role in workload identification in Confidential Computing and it is thus critically important to architect it in a way that best fits within the workload identity space.

This document builds upon and extends the TWI SIG Charter **\[1\]** to specify a comprehensive set of requirements that the Trustworthy Workload Identity Reference Architecture **\[2\]** must achieve.

The workload identity problem space can be defined by the following set of key scenarios and requirements:

1. The specifications of Workload Identities, Workload Credentials and authentication/authorization mechanisms developed by the TWI SIG must all be portable, standards-based, and capable of supporting transport-level and application-level scenarios, as well as federation.

2. Workloads can be deployed in a variety of environments:  
     
   * On-premises  
   * In the public cloud  
   * On remotely managed IoT devices  
   * On users’ personal devices  
       
3. Workloads can take a wide variety of shapes:  
     
   * Confidential virtual machines  
   * Confidential containers and Kubernetes pods  
   * Enclaves  
   * Serverless computing (lambdas, step functions, etc.)  
   * Cloud and on-premises services such as database services, queueing services and the like  
   * Managed services, including SaaS products  
       
4. Workloads might be standalone or composite (composite workloads being those consisting of multiple components from deployment through to decommissioning and potentially spanning devices or having components residing on the same physical device).  
     
5. A Workload may have multiple distinct credentials issued to it, e.g., for purposes of inbound, outbound and internal (component-to-component **\[ISSUE 1\]** for composite Workloads) authentication.  
     
6. Workload Instances might be short- or long-lived with different requirements applied to each kind:  
     
   * For short-lived Workloads, typified by container and serverless Workloads, it may be important to issue credentials quickly and reliably so as not to introduce performance and reliability penalties related to multiple and/or expensive round-trips involved in credential issuance.  
   * For long-lived Workloads, the lifetime of a Workload is likely to exceed that of the credentials issued to it, requiring ability to renew and/or revoke credentials in response to governance requirements and/or emerging threats without compromising overall system reliability.  
       
7. Workload Identity systems and solutions must be governable, including the following capabilities:  
     
   * Maintaining control over and a history of all policies and decisions involved in issuing and evaluating Workload Identities and Credentials.  
   * Ability to identify, isolate and remedy vulnerable Workloads in production quickly, such as when new threats are discovered or new attacks detected.  
   * Additional requirements to Workload Identity governance are specified in the forthcoming Workload Identity Governance pattern **\[3\].**  
       
8. Workload Identity must coexist in a predictable manner with regular datacenter maintenance operations such as workload migration.  
     
   * Operations that would change the Workload Credentials (e.g., by modifying some of the claims) should require redeployment where the old Workload is torn down and the new one stood up in its place with different Workload Credentials.  
   * Operations that would preserve the Workload Credentials, such as migration within the datacenter between equivalent hardware **\[ISSUE 2\]** instances, need not require new Workload Credentials to be issued.  
       
9. Workload Identity must handle the presence of reverse proxies including serially deployed reverse proxies.

   * If a client and a server talk over a series of reverse proxies with some doing traffic inspection (e.g., checking for malicious content), others modifying the traffic (redacting or adding content), what identities are then presented at each hop?  
     

So what does all that mean to Confidential Computing style Remote Attestation?

10. There must exist a set of easy to adopt mechanisms and patterns that use Remote Attestation in Workload Credential issuance, where the issued Workload Credentials are interoperable with those utilized by the rest of the Workload Identity ecosystem. As such, the interplay between RATS-style **\[4\]** Remote Attestation and industry standard identity providers and credential issuance mechanisms has to be investigated, documented and supported — across all Verifier implementations.  
      
11. While Remote Attestation mechanisms have access to the information needed to establish the Provenance of attesting Workloads, the mechanisms for binding this provenance to issued Workload Credentials need to be defined.  
      
12. Workloads ought to be able to both generate and certify their own keys, or obtain keys/credentials from secret key stores. The role of Remote Attestation in both of those cases has to be precisely defined and widely supported.  
      
13. Remote Attestation-based Workload Credential issuance has to be supported on a wide variety of computing platforms and mechanisms, including serverless and managed workloads, as well as solutions that do not strictly follow Confidential Computing “hardware-based” tenets, such as AWS Nitro[^1].  
      
14. Remote Attestation mechanisms for composite Workloads must be defined, including both composite workloads deployed on a single device (e.g., CPU \+ GPU \+ Smart NIC) and multiple devices (K8S pods/services, auto-scaling compute instances behind a load balancer, composite Workloads spanning devices yet sharing a lifetime, etc.)  
      
15. Issued Workload Credentials need to include claims about the Workload itself, as well as claims about the hosting environment (e.g. the physical location of the server on which the computation is taking place. Mechanisms for combining the results of Workload attestation with (securely sourced) hosting platform information resulting in Workload Credentials containing a combination of both kinds of claims need to be defined.

# Notes and Clarifications

This section goes over the requirements listed above and adds additional clarifications.

1. Requirement 1 states:

   *The specifications of Workload Identities, Workload Credentials and authentication/authorization mechanisms developed by the TWI SIG must all be portable, standards-based, and capable of supporting transport-level and application-level scenarios, as well as federation.*  
     
   In the case of Workloads developed in-house, integration of Workload Credential issuance with CI/CD mechanisms is highly desirable.  
     
   In the case of managed Workloads or Workloads obtained from trusted sources, other forms of provenance establishment can be used, with automation and accountability remaining key features  
     
   Both code itself and the configuration/environment in which the code is deployed are important in Workload Credential issuance; the environment may include attributes such as whether the code is deployed in test or production, what physical location the computation takes place in, and others.

     
2. Requirement 8 states: 

   *Workload Identity must coexist in a predictable manner with regular datacenter maintenance operations such as workload migration.*  
     
   1. *Operations that would change the Workload Credentials (e.g., by modifying some of the claims) should require redeployment where the old Workload is torn down and the new one stood up in its place with different Workload Credentials.*  
   2. *Operations that would preserve the Workload Credentials, such as migration within the datacenter between equivalent hardware instances, need not require new Workload Credentials to be issued.*

   Consider the following two nearly-equivalent cases: a workload is migrating from the Berlin datacenter to Frankfurt. In one case, the original Workload Credential contains a claim “Location \= Germany”. In the other case, the claim is “Location \= Berlin”. From the standpoint of this requirement, the first case does not change the claims of the Workload and the migration operation does not require the Credential to be reissued. In the second case, the migration operation must be treated as a re-deployment so that the Location claim can be updated to Frankfurt.

# Issues

TODO: Resolve all of these before we publish the final version of this document

1. Does this apply to different components implementing different "sub-services" and to e.g. CPU-to-GPU alike?  
2. Perhaps a definition (or examples) of "equivalent" or "inequivalent" would be helpful.  
   Here are some examples of platform operations that might change the security posture of a workload but that are unknown to the workload and can result in the workload unknowingly passing an identity with stale claims.  
   1. Workloads running on the same CPU/PSP when the CPU/PSP firmware is transparently updated to a new version.  
   2. Workloads migrated to different hardware (a different CPU) in the same rack running the same firmware.  
   3. Workloads migrated to hardware in a different datacenter (think "availability zone") in physically proximate geography and identical governmental jurisdiction.  
   4. Attestation evidence can become outdated at any time.  
   5. An attestation report can be requested at any time.  
   6. Attestation endorsement/verification can be performed at any time.

   This pattern suggests the need for leveraging identity token lifetime to allow customers to set how long they are willing to accept the risk of stale attestation evidence versus accepting the performance implications of frequent renewals to preserve freshness.

3. \<yogesh\>

# References

TODO: Replace temporary links with permanent ones

1. TWI SIG Charter [https://github.com/confidential-computing/governance/blob/main/SIGs/TWI/TWI\_Charter.md](https://github.com/confidential-computing/governance/blob/main/SIGs/TWI/TWI_Charter.md)  
2. TWI Reference Architecture [https://docs.google.com/document/d/1JWSQkzOcXofvOVUs3Xcq\_wBecZ4eQSmqXB-eWAxHq\_k/edit?usp=drive\_link](https://docs.google.com/document/d/1JWSQkzOcXofvOVUs3Xcq_wBecZ4eQSmqXB-eWAxHq_k/edit?usp=drive_link)  
3. Workload Identity Governance pattern TODO: add link  
4. RATS Architecture [https://datatracker.ietf.org/doc/html/rfc9334](https://datatracker.ietf.org/doc/html/rfc9334)

[^1]:  While AWS Nitro does not satisfy the strict definition of Confidential Computing, from the standpoint of the key requirement of the result of our work being broadly deployable, a major CSP cannot be ignored.