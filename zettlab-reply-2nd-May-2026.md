# Progress Update on Recent Key Issues

We would first like to offer our sincere apologies for the inconvenience, frustration, and concerns about product reliability caused by the recent hardware- and software-related issues.
Whatever the final root causes prove to be, the fact remains that the real-world experience delivered to some users has fallen short of the standard we expect of ourselves. That responsibility rests with us. We will not avoid it, nor will we soften it with vague language.
In response to the recent concerns around overall system design, thermal performance, backplane power delivery, and system stability, we immediately began a broader review of the design approach, internal validation logic, and relevant test data, while also extending the investigation across software, hardware, and supply chain processes.
This review has taken longer than we would have liked because, during the investigation, we found a meaningful gap between our internal test data and the real-world usage data reported by some users. That means we cannot rely only on the original design assumptions or laboratory results. We need to continue tracing the issue across the full delivery chain, including testing, supply chain handling, transportation, and real-world operating conditions, to determine whether the problems are tied to specific batches, localized hardware damage, software behavior in certain scenarios, or differences in actual usage environments.
Based on what we have reviewed so far, the original design targets and baseline validation results do not appear to show a fundamental deviation from the intended specifications. That said, this does not mean the issue can be dismissed simply because the original design passed internal validation. The clear gap between validation results and real-world user experience is exactly why we are continuing to investigate the full hardware delivery path, supply chain processes, and software behavior under actual usage conditions.
At this stage, our preliminary assessment is that the issue is unlikely to be attributable to a single failure point. Rather, it appears more likely to involve a combination of hardware-side delivery deviations and software-side strategy or scenario-specific limitations, with the effects becoming more pronounced in real user environments.
The information below should therefore be understood as a progress update on an ongoing investigation. We still need further recall analysis, supply chain trace-back, and broader retesting before we can reach final conclusions.

## I. Thermal-Related Issues

### 1. Thermal Interface Material Performance Differences
We have seen discussion in the community about whether differences in thermal interface materials may be affecting thermal performance. To verify this, we carried out comparative retesting together with our supply chain partners, using both the thermal material currently used by Zettlab and common alternatives that some users have tried as replacements.
As shown in the attached picture

### 2. Overall Thermal Behavior and Temperature Rise
For system cooling, we adopted a solution that has been widely used and validated in the market to keep the CPU operating within the specified thermal range. We have recently completed another round of retesting together with our supply chain partners.
At the same time, we also need to acknowledge that our earlier evaluation did not fully account for variation across real-world usage conditions and individual units. This is one of the important reasons why thermal performance in some actual user scenarios has differed from our expectations.
As shown in the attached picture
Based on our initial technical investigation, we have largely narrowed the issue to two primary directions:

* In some devices, the fan control strategy did not trigger as intended, reducing active cooling performance below the expected design level.
* In certain everyday usage scenarios, the CPU may experience intermittent abnormal load spikes, which can contribute additional heat.

When these two factors overlap, they can further amplify perceived heat and system stability fluctuations.

### 3. Software-Side Limitations Identified and Current Remediation Direction
During our internal investigation, we confirmed that the current system has gaps in its fan scheduling strategy and does not fully utilize available fan speed control capability, which can materially affect overall thermal performance.
To address this, we prioritized the following optimizations in version 1.9.0:

* Retuned the fan control curve
* Updated the kernel version
* Prioritized improvements to CPU thermal behavior
* Improved stability in certain abnormal load scenarios

We will continue refining scheduling logic and fan control behavior in subsequent updates.
We also want to clarify another software-side limitation that exists at present: the current CPU frequency scaling strategy supports only performance and powersave modes, and does not yet support more fine-grained governors such as userspace, ondemand, or conservative.
This creates two direct effects:

1. CPU frequency behavior can remain relatively aggressive, making it harder to return quickly to lower frequencies during normal use.
2. During intermittent background or burst workloads, the CPU may ramp up more readily, adding thermal load.

Going forward, we will continue refining the scheduling logic and kernel-level behavior, and gradually introduce more fine-grained dynamic frequency scaling so that we can better balance performance delivery and power and thermal control.
It is also important to note that software updates will primarily address thermal issues related to scheduling and system-level behavior. Devices that already have underlying hardware abnormalities will still need to be handled in conjunction with the results of the ongoing hardware investigation and subsequent support actions.

## II. Backplane Power and Overall Power Delivery Issues

### 1. Backplane Power Design
In response to community concerns about the backplane power design, we also carried out a further review.
According to publicly available specifications from major hard drive manufacturers, the power requirement of a single 24TB hard drive under read/write load is typically in the 8W–10W range.
WD:https://www.westerndigital.com/content/dam/doc-library/en_us/assets/public/western-digital/product/internal-drives/wd-gold/data-sheet-wd-gold-hdd.pdf
Seagate:https://www.seagate.com/content/dam/seagate/en/content-fragments/products/datasheets/exos-recertified/exos-recertified-1--en_US.pdf
Based on these reference specifications, we designed the system power architecture accordingly. The current backplane power capacity is 120W, which, even with additional headroom reserved, should theoretically be sufficient to support up to 8 populated drive bays.
From an interface design perspective, a standard PCIe edge connector is typically designed around 1.1A per pin. In our non-standard implementation, we use 9 pins, and similar non-standard approaches can also be found in a number of NAS system designs currently on the market.
Based on the information available so far, the current power design does not appear to show any obvious insufficiency at the specification level.

### 2. Backplane Power Instability
In response to reports from some users regarding SATA backplane power instability, we have preliminarily identified two possible contributing factors.
1) Physical Damage to the SATA Backplane During Logistics or Delivery
During the ongoing investigation, we found that, in a very small number of individual cases, the SATA backplane showed signs of abnormal physical damage. In those isolated cases, localized damage to the backplane may in turn lead to power instability, drive detection issues, or broader system instability.
Based on the information currently available, these cases appear more likely to be associated with external impact or compression during logistics and delivery. At this stage, we believe this is a low-incidence, isolated issue rather than a widespread one, and we will continue reviewing additional samples to better confirm its scope and characteristics.
2) Incorrect SATA Power-On Sequence Settings in BIOS During System Testing
We also found that some units did not have the correct SATA power-on sequence configured in BIOS during system testing. In third-party system environments, this can further amplify compatibility or power stability issues and may present as abnormal drive initialization or unstable drive detection.
We are continuing to expand sample review in order to determine the likely scope of impact and to assess how closely this issue is linked to the problems reported by users in the field.
Users may also verify whether their current BIOS settings are correct by following our setup guide. We will also provide an updated BIOS version later.

### 3. Current Assessment
We have also reviewed all previously recalled SATA PCB backplanes and confirmed that some of them showed signs of damage associated with logistics or other delivery-stage handling. At the same time, to determine whether the issue could have originated earlier in the process, we also inspected SATA PCB backplanes that had not yet been assembled into complete units.
At present, there does appear to be a gap between the actual condition of some delivered hardware and the condition of the test units used during validation. Supply-chain-related delivery and handling stages are therefore now one of the key focuses of our investigation.
However, we are still working to verify at which stage the abnormality occurs, which batches may be affected, and how strongly it correlates with the overall power stability issues reported in the field.

## III. Actions We Are Taking
### 1. Thermal Issues and Excessive Heat- or Power-Related Issues
We have already released version 1.9.0, which prioritizes improvements to CPU thermal behavior and system stability under certain abnormal load conditions by retuning the fan control curve and updating the kernel version.
That said, software updates will primarily address strategy- and scheduling-related issues. Devices that already have underlying hardware abnormalities will still need to be handled in conjunction with the results of ongoing hardware investigation and any follow-up remediation.

### 2. SATA PCB Backplane and Power Supply Issues

* Zettlab is actively working together with supply chain partners to continue investigating and validating each relevant stage, in order to ensure that delivered hardware matches the intended design specifications. This work still requires time, but we will continue pushing it forward and will share clearer conclusions as they become available.

* If you have experienced confirmed issues such as damaged backplane components, drive detection abnormalities, or unexpected drive drop-offs, please contact support@zettlab.com. We will assist with diagnosis and, for confirmed cases, arrange replacement SATA backplanes that have been verified.

* If you have experienced power instability or unexpected shutdown behavior, please contact us as soon as possible. Where necessary, we will also arrange recall and replacement of affected power adapters. This is important both for user support and for our joint root-cause investigation with supply chain partners.

* We will continue expanding the pool of hardware abnormality samples so that we can better establish the relationship between logistics-related damage, BIOS configuration issues, and actual failure behavior, and determine next steps based on confirmed findings.

## IV. Other Related Issues
### 1. Unstable Network Interface Behavior
Based on the current investigation, unstable network interface behavior appears in many cases to be related to hardware connection abnormalities. If the issue persists after a reboot, please contact support@zettlab.com for further diagnosis and support.
### 2. Power Button / Reset Strategy Adjustments
In response to user feedback that recovery options are limited when the system becomes unstable, we plan to introduce a system reset feature in the Q3 release to support system restore and reset operations.
### 3. Ultra eGPU Support
This feature currently requires BIOS-level enablement. We expect to make it accessible via SSH in version 1.10, and we will continue improving how ZettOS handles this workflow and the overall user experience. We are also preparing a new BIOS version so that this interface can be used correctly under third-party systems.
### 4. Front Panel Display Issues
We are also investigating reports of front panel display corruption, black screens, or display failure that cannot be recovered after reboot. At this stage, these issues appear preliminarily related to anomalies in certain display hardware batches. If you encounter this issue, please contact support@zettlab.com for further diagnosis and support.
### 5. ZettOS Stability Issues
At present, we have observed that many ZettOS instability reports are concentrated in the Ultra series. Our current preliminary assessment is that these issues are strongly related to overheating and related strategy limitations. With the thermal strategy adjustments introduced in the new version, we expect these issues to improve to some extent. Some internal testing on unreleased versions has already shown signs of improvement. We will continue monitoring the situation and releasing further optimizations.
### 6. Log-Related Concerns
We have also seen community discussion around logs, primarily focused on large log volume and insufficient desensitization of some log content. At present, logs are mainly used to record the runtime state of different ZettOS modules. They are generated and stored locally by default, and are only uploaded to our hosted platform if you explicitly choose to upload them.
At the same time, we acknowledge that some module logs may not yet be fully desensitized, which has understandably raised privacy concerns for some users. If you prefer, you may temporarily disable the User Improvement Program. We are also continuing to review and improve our logging strategy, and expect to roll out further improvements in versions planned for June–July.

## V. Principles for Follow-Up Handling
What this incident has revealed is not just a deviation in a single component, strategy, or process step. It has also highlighted shortcomings in our delivery consistency, supply chain control, issue early-warning mechanisms, and coverage of real-world user scenarios.
We also understand that user frustration stems not only from the issues themselves, but also from concerns about product reliability, response efficiency, and transparency. We will neither avoid those concerns nor treat them as something that can be addressed with a one-time statement or patch.
Going forward, we will continue tracing the relevant batches and issue chains, pushing corrective actions on both the hardware and software sides, and improving supply chain review, pre-shipment validation, and post-issue response mechanisms. For users who have already been affected, we will continue to provide corresponding support and follow-up handling.
If you encounter any related hardware or system issue, please contact support@zettlab.com first, and we will do our best to assist with diagnosis and resolution as quickly as possible.
Once again, we sincerely apologize to all affected users, and we also thank everyone for the continued feedback, questions, and scrutiny. It is precisely this kind of direct feedback that helps us identify issues more clearly, take responsibility more concretely, and carry out the necessary corrective work more thoroughly.
Zettlab Team

## Attatched Images

<img width="1280" height="664" alt="image" src="https://github.com/user-attachments/assets/3662566b-313d-4687-b813-c31db16bad33" />

<img width="1280" height="670" alt="image" src="https://github.com/user-attachments/assets/25bb7278-8d70-4cff-be89-8df3a0be476a" />

<img width="433" height="679" alt="image" src="https://github.com/user-attachments/assets/78ff9032-37ec-4e6e-8520-2456b6849691" />

<img width="1280" height="960" alt="image" src="https://github.com/user-attachments/assets/259fb6b6-2def-4a2e-a14a-e8475cffd6d8" />

<img width="861" height="4869" alt="image" src="https://github.com/user-attachments/assets/9a90a0b5-47ff-4357-9d32-1f27bf0513cb" />

<img width="1280" height="674" alt="image" src="https://github.com/user-attachments/assets/01b6385f-ced1-4e1b-8f55-3e83812e74ac" />

## Source
https://www.facebook.com/share/p/1CgSbfLktk/
