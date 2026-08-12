# How Audits Work, from Management Assertions to Control Matrices

## The Core Idea

An audit is not an auditor arriving and asking an organization to prove it is compliant. Before fieldwork begins, the organization prepares a body of material that defines what is being audited, what management claims to be true, and how those claims can be tested.

A useful way to think about the whole exercise is as a traceability system.

**Requirement -> Control -> Policy or Procedure -> Evidence -> Auditor Test -> Conclusion**

Every audit conclusion should trace back, link by link, to a requirement. The artifact that makes this traceability possible is the **control matrix**.

![The audit traceability chain](fig1-traceability-chain.svg)

## The Management Assertion

For attestation-style audits, management begins by defining the scope of the examination and making an assertion about the system being examined. The exact form depends on the framework. SOC examinations, for example, distinguish between SOC 1 and SOC 2, which address different subject matter (financial reporting controls versus security and operational criteria), and between Type 1 and Type 2 reports.

A **Type 1** report evaluates whether controls are suitably designed at a single point in time. A **Type 2** report goes further, evaluating whether those controls actually operated effectively over a defined period, typically six to twelve months.

Both are attestation engagements, which means both start the same way. Management makes an assertion. Stripped down, that assertion says something like *"ACME, Inc. operated its services in conformance with the applicable criteria."* The auditor's job is not to discover what the organization does from scratch. It is to test whether that assertion is fairly stated.

WebTrust for CAs engagements follow the same assertion-plus-period structure as a Type 2, which is why everything below maps cleanly onto the WebPKI.

This raises an obvious question for a CA that has not started issuing yet. It has no period to attest to. The Baseline Requirements handle that case in section 8.1 with a point-in-time readiness assessment, which evaluates whether the controls are designed and implemented rather than whether they operated. It must be completed no earlier than twelve months before the first publicly trusted certificate is issued, and a full audit has to follow within ninety days of that first issuance. Two things about it are easy to get wrong. It is not the same instrument as a point-in-time audit, which is typically used to confirm that findings in a qualified report have been remediated, and it does not substitute for a period audit. In the terms used here, a readiness assessment tests the matrix and the design of the controls, in advance of the operating evidence that only a period of running can produce.

It is also worth being precise about what a period audit delivers, because the word "period" can promise more than it means. The auditor does not observe the whole period. They test samples drawn from it, they do that work during and after the period, and then they issue a letter. By the time anyone reads it, the window it describes has closed.

![An audit is a retrospective](fig10-retrospective-window.svg)

Both halves of that picture matter. Inside the period, a clean opinion means the sampled tests passed, not that every issuance was examined. After the period, the letter is simply silent. A CA that was conformant through December and broke something in January holds an audit letter that is both accurate and useless for the question you actually have. This is the structural reason misissuance is usually caught by Certificate Transparency monitoring, linters, and problem reports rather than by audits, and it is the gap that continuous assurance exists to close.

## The Audit Artifact Hierarchy

The audit package can be understood as a hierarchy. Each layer answers a different question. What does management claim is true? Which services, systems, periods, and criteria are examined? How does the organization believe each requirement is satisfied? What does it say it does? What actually happened? Does the evidence support the claim?

![The audit artifact hierarchy](fig2-audit-hierarchy.svg)

One way to think about this is in terms of the **artifacts** an audit produces and consumes. Here is the structure mapped onto WebTrust for CAs.

![The artifact map for WebTrust for CAs](fig6-webtrust-artifact-map.svg)

Two things are worth noticing about this map. First, the WebPKI is unusual in that several of these artifacts are public. The assertion, the report, and the CPS are all published, and root programs consume them through the CCADB. Most SOC 2 artifacts never leave an NDA'd data room. Second, the one artifact in the list that is not public is the control matrix, which is exactly the artifact that explains how everything else connects. Relying parties see the commitments and the conclusion, but not the reasoning that links them.

## The Control Matrix Is the Map

The control matrix translates the audit criteria into something operational and testable. At a minimum, a row of the matrix commonly identifies the following.

| Element | Purpose |
|---|---|
| Requirement or criterion | What the audit framework requires |
| Control | What the organization does to satisfy the requirement |
| Control owner | Who is responsible for the control |
| Policy or procedure | Where the expected practice is documented |
| Evidence | What demonstrates that the control exists or operated |
| Test procedure | How the auditor can evaluate the control |
| Scope | Which systems, products, or services the control applies to |

The matrix is therefore not merely a checklist. It is an index into the organization's operating model. It is also two things at once, a navigation aid for the auditor and a representation of management's reasoning about why the organization believes it satisfies the requirements.

![The control matrix](fig8-control-matrix.svg)

Notice the policy source column. Only some rows resolve to the CPS. Others resolve to an IAM policy, an HR screening policy mapped to ISO 27001, or a training procedure that never appears in the public repository. Real matrices reach further still, into vendor management procedures, business continuity policies, change management procedures, and logging standards. The matrix is the only artifact that ties all of them back to the same set of requirements, which is why the auditor starts there.

What is important to understand is that this matrix is the scope of the audit. One matrix covers n policies and procedures, and it produces one audit letter, regardless of the size of n. That is the compression the audit performs. Hundreds of rows, dozens of documents, and thousands of evidence artifacts all collapse into a single opinion. The letter is one page. The matrix is why anyone can believe it.

At its core, the auditor is walking a traceability chain, once for every requirement in scope. The matrix points each requirement to a control, the document that commits to it, and the evidence that should exist if the control operated. The auditor walks each trace end to end and records a conclusion. The opinion is the aggregation of all of those completed traces.

A weak matrix hides scope gaps, ambiguous mappings, and unsupported controls. A strong one makes the whole compliance model explicit, and testable, in a single artifact.

## CP, CPS, and Where They Live

In the PKI, the two policy documents that matter most are the Certificate Policy and the Certification Practice Statement. The CP states the rules that govern; the CPS describes the practices used to satisfy them. Sometimes the two are collapsed into a single combined CP/CPS document. RFC 3647 gives both the same outline, which is what makes a combined document possible, and what makes it easy for scope to blur.

The CPS is where the CA's operational commitments live. Among other things, it describes identity validation practices, certificate issuance, certificate profiles, key generation and protection, revocation, incident handling, personnel controls, logging, system security, and repository practices. From an audit perspective it is the most important policy document in the matrix, because it contains the public, auditable statements about what the CA says it does.

There is a second, independent dimension, which is coverage. Sometimes a CP/CPS set is scoped to a single certificate type, or a narrow family of them. Other times a single monolithic document set covers everything the CA issues. These are two different axes, combined versus separate and monolithic versus scoped, and they are easy to conflate. We will come back to the second one, because it matters more than it looks.

These policy documents live somewhere, and that somewhere usually has change control on it. Someone tracks what changed, who changed it, when, and who approved it. The model is source control, and in some cases it literally is source control, a Git repository with reviews and tagged releases. That matters for the audit, because the repository is itself evidence. The change history demonstrates the document control the CPS claims, and a tagged release answers the question every incident eventually raises. Which version was in force on that date?

The period is also what makes the change history load-bearing rather than incidental. A Type 1 evaluates the document as it stands on a single day. A Type 2 evaluates every version that was in force across the period. If the CPS was revised ten times during the year, the scope is not one policy but ten, and the auditor uses the change record to reconstruct which version governed which span of time, then tests operations in each span against the version that governed it.

Sufficiency is re-asked at every version, not inherited. A version blessed in a prior period is not grandfathered in; if it carried forward unchanged it is reconsidered against criteria and a threat environment that may have moved underneath it, and if it changed, the diff is examined on its own terms. **So a single "abstract" policy in the scope column can expand into a sequence of concrete documents, each with its own window of authority and its own sufficiency question.** The scope is combinatorial in that sense, requirements times versions times the spans they governed, and the change-control record is the only thing that keeps it tractable. This is the operational reason the repository is evidence and not just documentation: without a reliable, timestamped history there is no way to say which version to test against which day, and the period audit loses its footing.

## Documentation Is Not Evidence

The policies themselves are not what gets audited. The audit tests actual practice against what the policies claim, and the instrument for that test is evidence, the records of what happened, collected and evaluated against each commitment.

![Policies say, evidence shows](fig5-policy-vs-evidence.svg)

A CPS statement that "CA private keys are generated and maintained in approved hardware security modules" supports the design of a control. The auditor still needs key ceremony records, HSM configuration, access logs, inventories, and change records to conclude the control operated.

**Policies establish expectations. Evidence establishes what happened. The control matrix connects the two.**

## Walking One Trace End to End

To make this concrete, consider a single trace. The TLS Baseline Requirements, in section 9.6.3, require the CA to bind its Subscribers to a set of obligations, including protecting their private keys and promptly requesting revocation on suspected compromise. The CA cannot perform this control itself. It can only impose it, and the instrument for imposing it is the Subscriber Agreement.

A CA that issues exclusively through ACME (RFC 8555) can turn that legal obligation into a structural control. ACME requires the account holder to accept the Subscriber Agreement before any order proceeds, and records that acceptance on the account. The CPS commits to this arrangement, and the Agreement itself explains the obligations to the Subscriber.

![One trace through the control matrix](fig7-subscriber-flowdown-trace.svg)

The auditor then walks the trace. They confirm that every issuance path goes through ACME with no side doors, that ACME refuses issuance without acceptance of the Agreement, and that the Agreement actually states what 9.6.3 requires. Evidence backs each check, from issuance logs to account records to the published Agreement with its version history.

The first check carries the weight. If any issuance path bypasses ACME, acceptance is no longer enforced by the protocol and the control degrades to a procedure someone has to remember to follow. That is the difference between a control enforced by protocol and one enforced by policy, and it is why ACME-only issuance is such a strong answer to this requirement.

This trace also exposes a real limit of the assurance model. The CA can evidence the operation of the control (the agreement was accepted) but not its effect. No CA audit verifies that subscribers actually protect their keys. The audit tests the flow-down, not the behavior, which is why the ecosystem backstops it with the CA-side revocation requirement in 4.9.1.1.

## Monolithic Versus Discrete CPS Documents

This takes us back to the coverage axis, discrete versus monolithic CPS documents. Historically, most CAs produced one CP/CPS set covering every certificate type they issued, because that is the easiest way to maintain it.

![Two ways to structure a CPS](fig3-monolithic-vs-discrete.svg)

The trade off is that a monolith is harder to understand, and not just because it is huge. Readers are left asking whether a given paragraph applies to TLS or to all certificate types, whether a validation method is used for S/MIME as well, and whether a revocation commitment covers code signing. Every one of those questions is a scope ambiguity, and in a monolith the scope column of the matrix keeps answering "all of them, sort of."

There is a deeper mechanism at work. To cover every case in one document, the wording of what the CA actually does has to be weakened. The requirements for TLS, S/MIME, and code signing are not the same, so a single sentence describing all of them can only describe them loosely. The result is language like "certificates are validated in accordance with applicable requirements," which is true for every service precisely because it commits to nothing about any of them. Weak wording is not a drafting failure. It is what a monolith requires.

And this connects directly back to the matrix, because vague commitments make weak rows. A control that maps to "in accordance with applicable requirements" gives the auditor almost nothing to test, so the monolith quietly degrades the quality of every trace that passes through it.

Discrete, service-scoped documents invert the trade. They are more work for the CA and clearer for everyone else, from relying parties to root programs to auditors to anyone diffing one version against the next. The documentation structure that is easiest for the operator to maintain is not the structure that is easiest for everyone else to understand and verify.

## The Root Programs Have Picked a Side

Recently both Chrome and Apple have pushed CAs in this direction. Chrome's policy says CP/CPS documents should be free standing and focused on a single use case, and it only accepts dedicated TLS hierarchies. Apple now requires single-purpose roots from applicants. Some CAs have used discrete CPS documents for years; others, like DigiCert, are only now making the change.

![The push to discrete](fig9-root-program-push.svg)

Note what this does to the audit. The matrix stays one matrix and the letter stays one letter, but the rows now point at commitments precise enough to test. The root programs are not asking for more documents for their own sake. They are asking for better rows. Scope precision is becoming a condition of trust.

## Why This Becomes an Automation Problem

Once a CA must maintain multiple service-specific CPS documents, document editing becomes a systems problem. Suppose five CPS documents each contain 80 pages of shared material and 40 pages of service-specific material. A single change to a common practice must be identified in every affected document, applied consistently, reviewed, approved, published, mapped into the matrix, and evidenced. Manual duplication makes drift a near certainty. One document says one thing while another retains older language. Even when the underlying practice is correct, the documentation becomes internally inconsistent, and that inconsistency is itself an assurance problem.

A more scalable model is to maintain common policy content as structured source material while producing separate, scoped outputs.

![Common source, discrete outputs](fig4-common-source-pipeline.svg)

Shared commitments are maintained once. Service-specific material remains independently scoped. The outputs are still discrete from the perspective of readers, auditors, and root programs, but a change to a shared commitment is made once at the source and lands consistently in every scoped output. The problem changes from document duplication to controlled document generation.

One consequence of that shift is worth stating plainly. Once the published documents are generated artifacts, the audit binds to the rendered output rather than to the source, so every published version has to be pinned and retained alongside the source revision it came from. The change control on the generation pipeline then becomes evidence in its own right, in exactly the way the document repository already was.

## Summary

A useful mental model for an audit runs as follows.

Management says what is true. The framework says what must be true. The control matrix explains why management believes it is true, and defines the scope of the audit. Policies and procedures, from the CPS to IAM, HR, vendor management, and business continuity documents, describe how the organization is supposed to operate. Evidence shows what actually happened. The auditor walks every trace, and the opinion is the aggregation of the results. One matrix, n documents, one letter, regardless of n.

The root programs are now demanding that the public end of this structure, the CPS, be scoped precisely enough to test. That improves assurance for the ecosystem and multiplies the documentation burden for the CA, which is why policy publication is becoming a structured, automated system rather than a collection of manually maintained documents. Modern assurance depends not just on having controls, but on maintaining reliable traceability between requirements, assertions, controls, documentation, evidence, and actual operation.
