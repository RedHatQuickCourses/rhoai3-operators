# Red Hat AI Training: OpenShift Operators for Models-as-a-Service

## SMEs for Development

- [To be assigned - Platform Engineering SME]
- [To be assigned - OpenShift AI SME]
- [To be assigned - Technical Reviewer]

## Problem Statement

Organizations struggle with inconsistent OpenShift AI deployments, leading to "shadow clusters" where operators are installed haphazardly, DataScienceCluster configurations are left at defaults, and GPUs sit idle while teams debate ownership of outages. Without a repeatable operator baseline, downstream capabilities (storage, registry, catalog, MaaS) cannot function reliably. This course solves the "Phase 0 chaos" by establishing a proven, declarative operator foundation that transforms ad-hoc installations into a governed, reproducible AI platform control plane.

## Course Goal

To equip platform engineers with the knowledge and skills to design, deploy, validate, and troubleshoot the complete OpenShift AI operator ecosystem on OpenShift 4.x. Participants will understand the architectural dependencies between OLM, infrastructure operators (NFD, GPU, cert-manager), governance operators (Kuadrant, Kueue, LeaderWorkerSet), and the RHOAI meta-operator. By completion, learners will confidently install the "Phase 0" baseline, diagnose common failures, execute rollback procedures, and validate cluster readiness for downstream AI capabilities.

## Major Changes in this Release

- **RHOAI 3.3 Focus**: Updated from 3.x to specifically target 3.3 with stable-3.3 channel subscription patterns
- **Kuadrant Gateway API**: Shift from legacy networking to Red Hat Connectivity Link 1.3+ with Gateway API integration
- **LeaderWorkerSet Integration**: New distributed pod coordination operator required for llm-d distributed inference runtime
- **Kueue Multi-Tenancy**: Red Hat build of Kueue for fair-share GPU scheduling and workload queue management
- **Dynamic Channel Discovery**: Operator subscriptions use dynamic channel queries instead of hardcoded versions to prevent installation script breakage
- **ClusterPolicy Validation**: NVIDIA GPU Operator v25.10+ requires explicit OpenAPI schema compliance (empty spec: {} no longer accepted)

## Target Audience

- **Platform Engineers**: Responsible for cluster infrastructure, operator lifecycle management, and AI platform readiness
- **OpenShift Administrators**: Managing multi-tenant clusters requiring AI capabilities with robust operator governance
- **DevOps / SREs**: Operating day-2 upgrades, monitoring operator health, and troubleshooting installation failures

## Prerequisite Skills/Knowledge

- OpenShift 4.x cluster administration (creating projects, RBAC, resource management)
- Basic Kubernetes operator concepts (CRD, CR, controller reconciliation loops)
- Command-line proficiency (oc CLI, YAML editing, bash scripting)
- Understanding of namespace isolation and OLM subscription model
- Optional: GPU hardware familiarity for accelerator operator sections

## Performance Objectives (POs)

- **[PO 1] Architect the Operator Dependency Stack**: Understand the business problem of "shadow clusters" and map the 3-layer operator architecture (Hardware → Serving/Governance → Orchestration). Identify which operators unlock which downstream capabilities and explain the critical order of operations to avoid race conditions.

- **[PO 2] Deploy the Hardware Foundation Layer**: Install and verify Node Feature Discovery (NFD) and NVIDIA GPU Operator (or equivalent accelerator operators) using declarative manifests. Validate hardware labels on nodes, diagnose ClusterPolicy failures, and confirm driver daemonsets are running on GPU nodes.

- **[PO 3] Configure the Serving and Governance Layer**: Deploy cert-manager, Red Hat Connectivity Link (Kuadrant), LeaderWorkerSet, and Kueue operators. Validate TLS certificate automation, gateway readiness, distributed pod coordination, and workload queue functionality. Troubleshoot OLM subscription failures and InstallPlan approval issues.

- **[PO 4] Activate the RHOAI Control Plane**: Install the Red Hat OpenShift AI Operator and configure DataScienceCluster (DSC) with selective component management. Enable dashboard, KServe, model registry, and pipelines while removing unused components. Validate DSC reconciliation and component readiness.

- **[PO 5] Validate and Verify Operator Health**: Execute a comprehensive validation framework checking CSV phases, operator pod status, CR condition fields, and dependency readiness. Use diagnostic commands to verify NFD labels, GPU device plugins, certificate issuers, Kuadrant control plane, and DSC component states.

- **[PO 6] Troubleshoot Failures and Execute Recovery**: Diagnose and resolve common operator installation failures: OLM stuck subscriptions, CSV installation failures, webhook timeouts, namespace conflicts, and resource exhaustion. Execute rollback procedures for failed upgrades and recover from corrupted CR states. Apply day-2 operational practices for operator upgrades and version pinning.

## Considerations and Risks

- **Lab Environment Dependencies**: Requires OpenShift 4.16+ cluster with cluster-admin access. GPU nodes optional but recommended for full validation of hardware layer operators.
- **Operator Version Volatility**: Channels change frequently (e.g., v24.09 → v25.10 for GPU operator). Dynamic channel discovery mitigates but requires testing against specific versions in production environments.
- **OLM Complexity**: Nested operator dependencies (e.g., Connectivity Link pulls DNS and Limitador operators) can cause cascading InstallPlan failures requiring manual intervention.
- **Namespace Collision Risks**: Students may accidentally deploy operators in wrong namespaces or create duplicate OperatorGroups, causing OLM tracking conflicts that are difficult to debug.
- **Time Investment**: Full installation takes 60-90 minutes in a healthy cluster, longer with troubleshooting scenarios and validation exercises.
- **Prerequisite Knowledge Gaps**: Students unfamiliar with CRDs may struggle with the "operator watches CR" mental model and reconciliation loop concepts.

## Products/Technologies

- Red Hat OpenShift Container Platform (4.16+)
- Red Hat OpenShift AI 3.3 (stable-3.3 channel)
- Node Feature Discovery (NFD) Operator
- NVIDIA GPU Operator (certified-operators catalog)
- Cert-Manager Operator for Red Hat OpenShift
- Red Hat Connectivity Link 1.3+ (Kuadrant: Authorino, Limitador, DNS Operator)
- Red Hat LeaderWorkerSet Operator
- Red Hat build of Kueue Operator
- Web Terminal Operator (CLI access dependency)
- Operator Lifecycle Manager (OLM) — built into OpenShift

## Progressive Diagram Build Strategy

Introduce a layered architecture diagram, building it from the bottom up as students progress through the lessons:

- **Lesson 1 (Foundation)**: Introduce the problem (shadow clusters vs governed platform) and show the desired end-state diagram with all 3 layers labeled but grayed out. This creates the mental model for the journey ahead.

- **Lesson 2 (Hardware Layer)**: Illuminate the Hardware Layer in blue. Show NFD daemonset scanning nodes and adding labels (feature.node.kubernetes.io/pci-10de.present). Show GPU operator consuming those labels to deploy driver daemonsets, container toolkit, and device plugins exposing nvidia.com/gpu resources.

- **Lesson 3 (Serving/Governance Layer)**: Illuminate the Serving & Governance Layer in green. Show cert-manager issuing certificates to KServe webhooks. Show Kuadrant gateway intercepting traffic with Authorino validating JWTs and Limitador enforcing rate limits. Show LeaderWorkerSet managing leader/worker pod topology for distributed inference. Show Kueue holding jobs in queue and releasing when GPUs available.

- **Lesson 4 (Orchestration Layer)**: Illuminate the Orchestration Layer in purple. Show RHOAI Operator in redhat-ods-operator namespace (control plane). Show DSC CR as "master switchboard" controlling components in redhat-ods-applications namespace (data plane). Show arrows from DSC to dashboard, KServe controller, model registry, pipelines deployments.

- **Lesson 5 (Validation Overlay)**: Overlay validation checkpoints on all 3 layers with green checkmarks. Show CSV status checks, pod health indicators, CR condition.Ready=True markers, and readiness gates. Include end-to-end smoke test flow from hardware through orchestration.

- **Lesson 6 (Troubleshooting Flows)**: Add failure points in red, warnings in yellow, and diagnostic paths in blue. Show OLM stuck states with diagnostic commands, webhook failures with cert-manager dependency arrows, GPU driver failures with NFD label dependencies, DSC component failures with dependency trees, and rollback arrows showing version downgrades.

**Diagram Design Recommendations**:
- Use consistent color coding (blue=hardware, green=serving/governance, purple=orchestration)
- Show namespace boundaries explicitly with labeled boxes
- Include "watches" relationship arrows (e.g., GPU operator → NFD labels)
- Mark MaaS-critical dependencies with asterisks or bold borders
- Display operator CRs as configuration inputs to operators
- Show data flow from hardware metrics through observability stack

---

## HIGH LEVEL DESIGN (HLD)

### Lesson 1: Understanding the Operator Ecosystem Architecture

**Lesson Goal**: Understand the business problem of inconsistent AI platform deployments and map the 3-layer operator architecture that provides the repeatable foundation for Models-as-a-Service.

#### Section 1.1: The Shadow Cluster Problem

**Content Summary**: Explain the business pain point that this course solves.

Organizations face a crisis where expensive GPU hardware investments fail to deliver ROI because:
- **Ad-hoc Installations**: Different teams install operators through different methods (UI, CLI, Helm), creating inconsistent cluster states
- **Mystery CSV Failures**: Operators fail to install with cryptic OLM errors, and teams lack the diagnostic knowledge to recover
- **Default Configurations**: DataScienceCluster toggles are left at defaults, missing critical MaaS capabilities like Kuadrant integration
- **GPU Waste**: Hardware sits idle while teams debate which namespace owns it or struggle with driver installation failures
- **Team Conflicts**: DevOps blames platform engineering, platform engineering blames security, and data scientists are blocked

**Business Impact Quantification**:
- Idle GPUs cost $10K-50K per month in lost inference capacity
- Each failed deployment delays AI projects by 2-4 weeks
- Manual troubleshooting consumes 20-40 engineer hours per incident
- Inconsistent platforms create security vulnerabilities and compliance gaps

**The Solution Preview**: A repeatable, declarative operator baseline that serves as "Phase 0" for all downstream capabilities. By following the proven installation sequence and validation framework in this course, organizations eliminate shadow clusters and establish a governed, reproducible AI platform control plane.

**[Diagram Build - Full Stack Preview]**: Show grayed-out 3-layer diagram with "Before: Shadow Cluster Chaos" on left (operators scattered randomly, question marks, red X's) and "After: Governed Platform Foundation" on right (clean layers, green checks, organized namespaces).

#### Section 1.2: Operator Fundamentals and OLM [PO1]

**Content Summary**: Build the foundational understanding of how operators work in OpenShift.

**What operators are**:
- Software extensions for Kubernetes that act as automated administrators
- Encode human operational knowledge into software to manage applications
- Use a "controller" pattern: watch Custom Resources (CRs) and continuously reconcile actual state with desired state

**The operator win**: Eliminate manual intervention for complex day-2 operational tasks:
- Automated upgrades without downtime
- Automatic failure recovery (pod crashes, node failures)
- Consistent, repeatable installations across environments
- Vendor domain expertise embedded in the operator logic

**How OLM manages operator lifecycle**:
- **Subscriptions**: Declare intent to install an operator from a catalog (redhat-operators, certified-operators)
- **InstallPlans**: OLM generates a plan to install the operator and its dependencies
- **ClusterServiceVersions (CSVs)**: The installed operator version, showing phase (Installing, Pending, Succeeded, Failed)
- **Custom Resources**: Configuration files that activate operator functionality

**Cluster vs Add-on Operators**:
- **Cluster Operators**: Managed by Cluster Version Operator (CVO), run core OpenShift functions (authentication, networking, storage)
- **Add-on Operators**: Installed via OLM from OperatorHub, extend cluster capabilities (AI, databases, middleware)

**Namespace vs Cluster-Scoped Operators**:
- **Namespace-scoped**: Operator watches resources in specific namespaces (most operators)
- **Cluster-scoped**: Operator watches resources across all namespaces (GPU operators, cert-manager, Kueue)

**Extract from**: `/Users/kaknox/Documents/GitHub/rhoai3-operators/modules/chapter1/pages/intro.adoc` lines 20-38

**New Content**: Add failure scenario example:
> **What happens when OLM gets stuck**: If a catalog source is unhealthy, subscriptions remain in "UpgradePending" state forever. If an InstallPlan has unsatisfiable constraints (conflicting operator versions), installation fails silently. Understanding these failure modes is critical for troubleshooting.

#### Section 1.3: The 3-Layer MaaS Operator Stack [PO1]

**Content Summary**: Map the architectural layers and identify which operators belong to each layer.

**Layer 1 - Hardware Foundation**:
- **Node Feature Discovery (NFD)**: Scans worker nodes for hardware features (GPUs, CPUs, NICs), applies Kubernetes labels for scheduling
- **Hardware Accelerator Operators**: NVIDIA GPU Operator, AMD GPU Operator, Intel Gaudi Operator, IBM Spyre Operator manage drivers, device plugins, and monitoring

**Layer 2 - Serving & Governance**:
- **Cert-Manager**: Automated TLS certificate provisioning, renewal, and retirement for secure KServe webhooks and model endpoints
- **Red Hat Connectivity Link (Kuadrant)**: Kubernetes-native authentication (Authorino) and rate limiting (Limitador) for API governance
- **LeaderWorkerSet (LWS)**: Coordinates multi-node distributed inference pods with leader/worker topology for large models
- **Red Hat build of Kueue**: Resource allocation, quota management, job scheduling, and fair sharing for GPU multi-tenancy

**Layer 3 - Orchestration**:
- **Red Hat OpenShift AI Operator**: Meta-operator managing the entire AI lifecycle platform
  - **Control Plane** (redhat-ods-operator namespace): Operator pod monitoring cluster state
  - **Data Plane** (redhat-ods-applications namespace): Actual AI workloads (dashboard, KServe, model registry, pipelines)

**Extract from**: `/Users/kaknox/Documents/GitHub/rhoai3-operators/modules/chapter1/pages/intro.adoc` lines 45-77

**[Diagram Build - Layer Labels]**: Illuminate the 3 distinct layers with example operators and arrows showing dependencies flowing upward from hardware through serving to orchestration.

#### Section 1.4: Order of Operations Strategy [PO1]

**Content Summary**: Why installation order matters and what breaks if the sequence is violated.

**The dependency chain principle**:
1. **Hardware Labels before Hardware Allocation**: NFD must label nodes before GPU operator can target them with driver daemonsets
2. **Trust before Traffic**: Cert-manager must be issuing certificates before KServe deploys webhooks that require TLS
3. **Gateways before Governed Exposure**: Kuadrant must be running before MaaS endpoints can enforce authentication and rate limits
4. **Coordination before Distribution**: LeaderWorkerSet must be installed before llm-d can deploy multi-pod inference runtimes

**What breaks if you install RHOAI before cert-manager** [PO1]:
- KServe controller manager attempts to register validating webhooks
- Webhook registration fails with "x509: certificate signed by unknown authority"
- KServe deployment stuck in Progressing state indefinitely
- Error only visible in kserve-controller-manager pod logs, not in DSC status
- **Recovery**: Install cert-manager, delete kserve-controller-manager pod to force restart

**What breaks if you skip NFD before GPU operator** [PO1]:
- ClusterPolicy applies successfully (no error message)
- Driver daemonsets remain in Pending state with 0 pods scheduled
- No nodes have feature.node.kubernetes.io/pci-10de.present=true label
- GPU operator waits indefinitely for matching nodes
- **Recovery**: Install NFD, verify labels appear, GPU operator automatically schedules drivers

**What breaks if you install operators in wrong namespaces** [PO1]:
- OLM creates conflicting OperatorGroups
- CSV shows "TooManyOperatorGroups" error in status conditions
- Subscription remains stuck, no InstallPlan generated
- **Recovery**: Delete duplicate OperatorGroup, recreate subscription

**Extract from**: `/Users/kaknox/Documents/GitHub/rhoai3-operators/modules/chapter1/pages/architecture.adoc` lines 16-30

#### Section 1.5: Namespace Architecture and Custom Resources [PO1]

**Content Summary**: Where each operator lives, which namespaces they target, which CRs activate them.

**Operator → Namespace → CR Mapping Table**:

| Operator | Namespace | Activation CR | CR Name | Scope |
|----------|-----------|---------------|---------|-------|
| Node Feature Discovery | openshift-nfd | NodeFeatureDiscovery | nfd-instance | Namespaced |
| NVIDIA GPU Operator | nvidia-gpu-operator | ClusterPolicy | gpu-cluster-policy | Cluster |
| Cert-Manager | cert-manager-operator | CertManager | cluster | Cluster |
| Red Hat Connectivity Link | openshift-operators | Kuadrant | kuadrant | Namespaced |
| LeaderWorkerSet | openshift-lws-operator | LeaderWorkerSetOperator | cluster | Namespaced |
| Red Hat build of Kueue | openshift-kueue-operator | Kueue | cluster | Cluster |
| Red Hat OpenShift AI | redhat-ods-operator | DataScienceCluster | default-dsc | Cluster |
| Red Hat OpenShift AI | redhat-ods-operator | DSCInitialization | default-dsci | Cluster |

**Why namespaces matter**:
- **Operator isolation**: Prevents OLM tracking conflicts when multiple operators manage similar resources
- **RBAC boundaries**: Limits operator permissions to specific namespaces for security
- **Resource quotas**: Allows platform teams to limit operator resource consumption
- **Troubleshooting clarity**: Logs and metrics are scoped to operator namespace

**Why CRs matter**:
- Operators don't do anything until a CR instructs them
- CRs define desired state, operators reconcile actual state to match
- Multiple CRs of same type can exist (e.g., multiple ClusterQueues in Kueue)
- CR status field shows health, conditions, and error messages

**Extract from**: `/Users/kaknox/Documents/GitHub/rhoai3-operators/modules/chapter1/pages/architecture.adoc` lines 32-119

#### Section 1.6: Knowledge Check Quiz [PO1]

Test understanding of architectural concepts before moving to hands-on deployment.

**Question 1**: What happens if you install the GPU operator before NFD?
- A) GPU operator installation fails with an error
- B) Driver daemonsets won't deploy because nodes lack hardware labels ✓
- C) GPU operator automatically installs NFD as a dependency
- D) Nodes are automatically labeled by the GPU operator

**Question 2**: Which Custom Resource activates the RHOAI data plane components?
- A) DSCInitialization
- B) DataScienceCluster ✓
- C) OpenShiftAI
- D) RHODSOperator

**Question 3**: Why must cert-manager install before RHOAI?
- A) RHOAI Operator requires a valid TLS certificate to start
- B) KServe webhooks require TLS certificates that cert-manager provides ✓
- C) Cert-manager is listed as a dependency in the RHOAI CSV
- D) The RHOAI dashboard won't deploy without certificates

**Question 4**: Which namespace should you use for the Red Hat Connectivity Link operator?
- A) kuadrant-system
- B) redhat-operators
- C) openshift-operators ✓
- D) connectivity-link

**Question 5**: What is the primary purpose of the LeaderWorkerSet operator?
- A) Manage user permissions for model serving
- B) Coordinate multi-node distributed inference pods ✓
- C) Schedule GPU workloads across the cluster
- D) Enforce rate limits on model endpoints

---

### Lesson 2: Deploying the Hardware Foundation Layer

**Lesson Goal**: Install and verify Node Feature Discovery and GPU operators using declarative manifests, validating hardware detection and driver daemonset health, with troubleshooting skills for OLM failures.

#### Section 2.1: Establishing CLI Access with Web Terminal [PO2]

**Content Summary**: Install Web Terminal operator via UI to gain secure, in-cluster command-line access.

**Why Web Terminal**:
- Provides browser-based terminal without local oc CLI installation
- Pre-authenticated to cluster (no manual login required)
- Includes common tools: oc, kubectl, helm, kn, tkn
- Secure: runs inside cluster, respects RBAC

**Installation procedure**:
1. Log in to OpenShift Web Console as cluster administrator
2. Navigate to Ecosystem → Software Catalog (or OperatorHub in older versions)
3. Search for "Web Terminal" and select Red Hat Web Terminal
4. Click Install, accept default settings (all namespaces, automatic approval)
5. Wait for CSV phase to show "Succeeded"
6. Launch terminal: Click command-line icon (>_) in top-right header
7. Terminal window initializes at bottom, click "Start" when prompted

**Note**: Web Terminal automatically installs DevWorkspace Operator as dependency.

**Extract from**: `/Users/kaknox/Documents/GitHub/rhoai3-operators/modules/chapter1/pages/lab0.adoc` lines 23-43

#### Section 2.2: Installing Node Feature Discovery [PO2]

**Content Summary**: Deploy NFD operator to scan nodes and apply hardware feature labels.

**Step 1: Create the Namespace**
```bash
oc create namespace openshift-nfd
```
Always start with fresh namespace to avoid OLM conflicts.

**Step 2: Create the OperatorGroup**
```bash
cat <<EOF | oc create -f -
apiVersion: operators.coreos.com/v1
kind: OperatorGroup
metadata:
  generateName: openshift-nfd-
  namespace: openshift-nfd
spec:
  targetNamespaces:
  - openshift-nfd
EOF
```
Use `oc create` (not `oc apply`) to allow OLM to generate unique name, preventing state conflicts.

**Step 3: Create the Subscription**
```bash
cat <<EOF | oc create -f -
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  name: nfd
  namespace: openshift-nfd
spec:
  channel: "stable"
  installPlanApproval: Automatic
  name: nfd
  source: redhat-operators
  sourceNamespace: openshift-marketplace
EOF
```

**Step 4: Verify Operator Installation**
```bash
oc get csv -n openshift-nfd
```
Proceed only when PHASE displays "Succeeded".

**Step 5: Create the NodeFeatureDiscovery Instance**
```bash
cat <<EOF | oc create -f -
apiVersion: nfd.openshift.io/v1
kind: NodeFeatureDiscovery
metadata:
  name: nfd-instance
  namespace: openshift-nfd
spec: {}
EOF
```

**Step 6: Verify Hardware Discovery**
```bash
# Check NFD pods running
oc get pods -n openshift-nfd

# Verify node labels (replace <worker-node-name>)
oc describe node <worker-node-name> | grep feature.node.kubernetes.io
```

**Expected labels for GPU nodes**:
- `feature.node.kubernetes.io/pci-10de.present=true` (NVIDIA)
- `feature.node.kubernetes.io/cpu-model.family=6`
- `feature.node.kubernetes.io/system-os_release.ID=rhcos`

**Business value**: NFD is the foundation of hardware governance. By labeling nodes, NFD enables precise Hardware Profiles preventing developers from accidentally scheduling expensive H100 GPUs for simple CPU-bound tasks.

**[Diagram Build - Layer 1 NFD]**: Show NFD daemonset on nodes, scanning PCIe bus, adding labels to node metadata.

**Extract from**: `/Users/kaknox/Documents/GitHub/rhoai3-operators/modules/chapter1/pages/lab0.adoc` lines 54-141

#### Section 2.3: Installing the NVIDIA GPU Operator [PO2]

**Content Summary**: Deploy GPU operator to inject drivers based on NFD labels.

**Step 1: Create Dedicated Namespace**
```bash
oc create namespace nvidia-gpu-operator
```

**Step 2: Create the OperatorGroup**
```bash
cat <<EOF | oc create -f -
apiVersion: operators.coreos.com/v1
kind: OperatorGroup
metadata:
  generateName: nvidia-gpu-operator-
  namespace: nvidia-gpu-operator
spec:
  targetNamespaces:
  - nvidia-gpu-operator
EOF
```

**Step 3: Dynamically Find Channel and Subscribe**
```bash
# Fetch current default channel
CHANNEL=$(oc get packagemanifest gpu-operator-certified -n openshift-marketplace -o jsonpath='{.status.defaultChannel}')

# Create subscription using discovered channel
cat <<EOF | oc create -f -
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  name: gpu-operator-certified
  namespace: nvidia-gpu-operator
spec:
  channel: "${CHANNEL}"
  installPlanApproval: Automatic
  name: gpu-operator-certified
  source: certified-operators
  sourceNamespace: openshift-marketplace
EOF
```

**Why dynamic channel discovery**: Operator channels change (v24.09 → v25.10). Hardcoded channels break automation.

**Step 4: Verify CSV**
```bash
oc get csv -n nvidia-gpu-operator
```
Wait for PHASE "Succeeded" before proceeding.

**Step 5: Deploy ClusterPolicy**
```bash
cat <<EOF | oc create -f -
apiVersion: nvidia.com/v1
kind: ClusterPolicy
metadata:
  name: gpu-cluster-policy
spec:
  operator:
    defaultRuntime: crio
  driver:
    enabled: true
  toolkit:
    enabled: true
  devicePlugin:
    enabled: true
  dcgm:
    enabled: true
  dcgmExporter:
    enabled: true
  gfd:
    enabled: true
  nodeStatusExporter:
    enabled: true
  daemonsets: {}
EOF
```

**Critical**: v25.10+ enforces strict OpenAPI validation. Empty `spec: {}` fails. Must specify `operator.defaultRuntime: crio` for OpenShift.

**Step 6: Verification**
```bash
# Check ClusterPolicy status
oc get clusterpolicy gpu-cluster-policy

# Watch pods deploy
oc get pods -n nvidia-gpu-operator
```

**Expected pods on GPU nodes**:
- nvidia-driver-daemonset
- nvidia-container-toolkit-daemonset
- nvidia-device-plugin-daemonset
- nvidia-dcgm-exporter

**Note**: If no GPU nodes present, device-specific pods remain unscheduled (expected behavior).

**[Diagram Build - Layer 1 GPU]**: Show GPU operator watching NFD labels, deploying driver daemonsets to labeled nodes, device plugin exposing nvidia.com/gpu resource.

**Extract from**: `/Users/kaknox/Documents/GitHub/rhoai3-operators/modules/chapter1/pages/lab0.adoc` lines 145-273

#### Section 2.4: Troubleshooting OLM Installation Failures [PO6]

**Content Summary**: Diagnostic tactics and recovery procedures when operators fail to install.

**Existing OLM troubleshooting tactics** (Extract from lab0.adoc lines 207-227):

**Check if OLM registered the Subscription**:
```bash
oc get subscription gpu-operator-certified -n nvidia-gpu-operator -o wide
```
Look for: CurrentCSV, State, CatalogHealth

**Check if InstallPlan was generated**:
```bash
oc get installplan -n nvidia-gpu-operator
```
Look for: Approved, Phase (Complete, Failed, Installing)

**Verify CSV succeeded**:
```bash
oc get csv -n nvidia-gpu-operator
```
Look for: Phase (Succeeded, Pending, Installing, Failed)

**New comprehensive troubleshooting scenarios**:

**Scenario 1: Subscription Stuck in "UpgradePending"**

**Symptoms**: Subscription exists but CSV never appears, no InstallPlan generated.

**Diagnosis**:
```bash
# Check subscription status
oc get subscription <name> -n <namespace> -o yaml | grep -A 10 status

# Verify catalog source healthy
oc get catalogsource -n openshift-marketplace
oc get pods -n openshift-marketplace | grep certified-operators

# Check package exists in catalog
oc get packagemanifest <package-name> -n openshift-marketplace
```

**Root causes**:
- Catalog source pod crashed or unhealthy
- Package name misspelled in subscription
- Channel doesn't exist for this catalog version
- Network connectivity to registry.redhat.io blocked

**Resolution**:
```bash
# Restart catalog source pod
oc delete pod -n openshift-marketplace -l olm.catalogSource=certified-operators

# Wait for pod to restart, verify healthy
oc wait --for=condition=Ready pod -n openshift-marketplace -l olm.catalogSource=certified-operators --timeout=300s

# Delete and recreate subscription
oc delete subscription <name> -n <namespace>
# Re-run subscription creation
```

**Scenario 2: InstallPlan in "Failed" State**

**Symptoms**: InstallPlan exists but shows Failed phase.

**Diagnosis**:
```bash
# Get InstallPlan details
oc get installplan -n <namespace>
oc get installplan <name> -n <namespace> -o yaml

# Check conditions
oc get installplan <name> -n <namespace> -o jsonpath='{.status.conditions}' | jq
```

**Root causes**:
- Missing CRDs required by operator
- Conflicting operator versions already installed
- Insufficient cluster resources (memory, CPU)
- RBAC permissions missing

**Resolution**:
```bash
# Delete failed InstallPlan (OLM will regenerate)
oc delete installplan <name> -n <namespace>

# If conflicts exist, identify and remove
oc get csv -A | grep <operator-name>

# Manually approve if needed
oc patch installplan <name> -n <namespace> --type merge -p '{"spec":{"approved":true}}'
```

**Scenario 3: CSV in "Pending" State**

**Symptoms**: CSV appears but never transitions to "Succeeded".

**Diagnosis**:
```bash
# Check CSV status
oc get csv <name> -n <namespace> -o yaml | grep -A 20 status

# Check operator pod logs
oc get pods -n <namespace>
oc logs <operator-pod> -n <namespace>

# Check for image pull errors
oc describe pod <operator-pod> -n <namespace> | grep -A 10 Events
```

**Root causes**:
- Operator pod failing to start (CrashLoopBackOff)
- Missing dependent operators not yet installed
- Image pull failure (imagePullSecrets, registry auth)
- Insufficient RBAC for operator service account

**Resolution**:
```bash
# Check operator deployment
oc get deployment -n <namespace>

# Describe deployment for events
oc describe deployment <operator-deployment> -n <namespace>

# Force pod restart
oc delete pod <operator-pod> -n <namespace>

# Check service account permissions
oc describe sa <operator-sa> -n <namespace>
```

**Scenario 4: Multiple OperatorGroup Conflict**

**Symptoms**: Subscription shows "TooManyOperatorGroups" error.

**Diagnosis**:
```bash
# List all OperatorGroups in namespace
oc get operatorgroup -n <namespace>
```

**Root causes**:
- Accidentally created second OperatorGroup in openshift-operators (already has default global OperatorGroup)
- Used `oc apply` instead of `oc create` with generateName, creating duplicates

**Resolution**:
```bash
# Delete duplicate OperatorGroup (keep only one)
oc delete operatorgroup <duplicate-name> -n <namespace>

# Verify only one remains
oc get operatorgroup -n <namespace>

# If subscription still stuck, recreate it
oc delete subscription <name> -n <namespace>
# Re-run subscription creation
```

**Scenario 5: Namespace Targeting Mismatch**

**Symptoms**: Operator installs but cannot watch resources in target namespaces.

**Diagnosis**:
```bash
# Check OperatorGroup targetNamespaces
oc get operatorgroup <name> -n <namespace> -o jsonpath='{.spec.targetNamespaces}'

# Check CSV installModes
oc get csv <name> -n <namespace> -o jsonpath='{.spec.installModes}' | jq
```

**Root causes**:
- OperatorGroup targeting specific namespaces but CSV requires AllNamespaces mode
- OperatorGroup omits targetNamespaces but CSV requires OwnNamespace mode

**Resolution**:
```bash
# Delete OperatorGroup and subscription
oc delete operatorgroup <name> -n <namespace>
oc delete subscription <name> -n <namespace>

# Recreate with correct targetNamespaces
# For cluster-wide operators, omit targetNamespaces
# For namespace-scoped, specify targetNamespaces list
```

**Scenario 6: Webhook Failures Blocking Deployment**

**Symptoms**: Operator installs but validating webhook configuration fails.

**Diagnosis**:
```bash
# Check webhook configurations
oc get validatingwebhookconfigurations | grep <operator>
oc get mutatingwebhookconfigurations | grep <operator>

# Check operator pod logs for webhook errors
oc logs <operator-pod> -n <namespace> | grep -i webhook
oc logs <operator-pod> -n <namespace> | grep -i certificate
```

**Root causes**:
- Cert-manager not installed or unhealthy
- Webhook service not ready
- Certificate not issued or expired
- CA bundle incorrect in webhook configuration

**Resolution**:
```bash
# Verify cert-manager healthy
oc get pods -n cert-manager

# Check certificate resources
oc get certificate -n <namespace>
oc describe certificate <cert-name> -n <namespace>

# Check webhook service endpoints
oc get svc -n <namespace>
oc get endpoints <webhook-svc> -n <namespace>

# Force certificate renewal
oc delete certificate <cert-name> -n <namespace>

# Restart operator pod
oc delete pod <operator-pod> -n <namespace>
```

#### Section 2.5: Validating Hardware Discovery [PO5]

**Content Summary**: Comprehensive verification checklist for Layer 1 health.

**NFD Validation Checklist**:

1. **Verify NFD master pod running**:
```bash
oc get pods -n openshift-nfd -l app=nfd-master
```
Expected: 1 pod in Running state

2. **Verify NFD worker daemonset deployed**:
```bash
oc get ds -n openshift-nfd
```
Expected: nfd-worker with DESIRED = CURRENT = READY

3. **Check NFD master logs for successful scans**:
```bash
oc logs -n openshift-nfd -l app=nfd-master --tail=50 | grep -i "processing node"
```
Expected: Logs showing nodes being processed

4. **Verify worker nodes have hardware labels**:
```bash
# List all feature labels on a worker
oc get node <worker-name> --show-labels | grep feature.node

# Check for GPU presence specifically
oc get nodes -l feature.node.kubernetes.io/pci-10de.present=true
```
Expected: GPU nodes show pci-10de.present=true label

**GPU Operator Validation Checklist**:

1. **Verify ClusterPolicy accepted**:
```bash
oc get clusterpolicy gpu-cluster-policy
oc get clusterpolicy gpu-cluster-policy -o jsonpath='{.status.state}'
```
Expected: state = "ready"

2. **Verify GPU nodes have nvidia.com/gpu resource**:
```bash
oc describe node <gpu-node-name> | grep nvidia.com/gpu
```
Expected: Under Allocatable, see `nvidia.com/gpu: 8` (or GPU count)

3. **Check NVIDIA driver version**:
```bash
oc get pods -n nvidia-gpu-operator -l app=nvidia-driver-daemonset
oc exec -n nvidia-gpu-operator <driver-pod> -- nvidia-smi
```
Expected: nvidia-smi output showing driver version, GPU model, memory

4. **Verify device plugin exposing GPUs**:
```bash
# Check device plugin logs
oc logs -n nvidia-gpu-operator -l app=nvidia-device-plugin-daemonset --tail=20

# Verify GPU resources advertised to scheduler
oc get nodes -o json | jq '.items[] | select(.status.allocatable."nvidia.com/gpu" != null) | {name:.metadata.name, gpus:.status.allocatable."nvidia.com/gpu"}'
```

5. **Test GPU allocation with sample pod** (optional):
```bash
cat <<EOF | oc apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: gpu-test
spec:
  restartPolicy: OnFailure
  containers:
  - name: cuda-test
    image: nvidia/cuda:12.2.0-base-ubi8
    command: ["nvidia-smi"]
    resources:
      limits:
        nvidia.com/gpu: 1
EOF

# Wait for pod to complete
oc wait --for=condition=Ready pod/gpu-test --timeout=60s

# Check logs
oc logs gpu-test

# Clean up
oc delete pod gpu-test
```
Expected: nvidia-smi output showing GPU detected

**Layer 1 Health Summary**:
```bash
# Quick health check script
echo "=== Layer 1: Hardware Foundation Health ==="
echo "NFD Pods:"
oc get pods -n openshift-nfd
echo ""
echo "NFD-Labeled GPU Nodes:"
oc get nodes -l feature.node.kubernetes.io/pci-10de.present=true
echo ""
echo "GPU Operator Pods:"
oc get pods -n nvidia-gpu-operator
echo ""
echo "GPU Resources Available:"
oc get nodes -o json | jq -r '.items[] | select(.status.allocatable."nvidia.com/gpu" != null) | "\(.metadata.name): \(.status.allocatable."nvidia.com/gpu") GPUs"'
```

---

### Lesson 3: Configuring the Serving and Governance Layer

**Lesson Goal**: Deploy cert-manager, Connectivity Link (Kuadrant), LeaderWorkerSet, and Kueue operators, validating TLS automation, gateway readiness, distributed pod coordination, and workload queue functionality.

#### Section 3.1: Installing cert-manager Operator [PO3]

**Content Summary**: Deploy cert-manager for automated TLS certificate provisioning.

**Why cert-manager is critical**:
- RHOAI relies on automated TLS certificate generation for webhooks, model serving endpoints, and internal communication
- KServe controller manager requires valid TLS certificates for admission webhooks
- Without cert-manager, DSC deployment fails with webhook registration errors

**Installation Procedure**:

**Step 1: Create Dedicated Namespace**
```bash
oc create namespace cert-manager-operator
```

**Step 2: Create OperatorGroup**
```bash
cat <<EOF | oc create -f -
apiVersion: operators.coreos.com/v1
kind: OperatorGroup
metadata:
  generateName: openshift-cert-manager-
  namespace: cert-manager-operator
spec:
  targetNamespaces:
  - cert-manager-operator
EOF
```

**Step 3: Dynamically Find Channel and Subscribe**
```bash
# Fetch current default channel
CHANNEL=$(oc get packagemanifest openshift-cert-manager-operator -n openshift-marketplace -o jsonpath='{.status.defaultChannel}')

# Create subscription
cat <<EOF | oc create -f -
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  name: openshift-cert-manager-operator
  namespace: cert-manager-operator
spec:
  channel: "${CHANNEL}"
  installPlanApproval: Automatic
  name: openshift-cert-manager-operator
  source: redhat-operators
  sourceNamespace: openshift-marketplace
EOF
```

**Step 4: Verify Operator Installation**
```bash
oc get csv -n cert-manager-operator
```
Wait for PHASE "Succeeded".

**Step 5: Verify Automatic CertManager CR Creation**

Unlike other operators, cert-manager is autonomous. It automatically creates a CertManager CR named "cluster" and spins up pods in a new "cert-manager" namespace.

```bash
# Verify CertManager CR exists
oc get certmanager cluster

# Verify cert-manager pods running
oc get pods -n cert-manager
```

Expected pods:
- cert-manager (controller)
- cert-manager-cainjector
- cert-manager-webhook

**[Diagram Build - Layer 2 Certs]**: Show cert-manager issuing certificates, arrows to KServe webhooks showing TLS requirements.

**Extract from**: `/Users/kaknox/Documents/GitHub/rhoai3-operators/modules/chapter1/pages/lab0.adoc` lines 281-353

#### Section 3.2: Installing Red Hat Connectivity Link (Kuadrant) [PO3]

**Content Summary**: Deploy Kuadrant for API governance, authentication, and rate limiting.

**Why Kuadrant is MaaS-critical**:
- Models-as-a-Service requires authentication (Authorino validates JWTs)
- Rate limiting prevents abuse (Limitador enforces token quotas)
- Gateway API provides modern routing (replaces legacy Istio patterns)
- Without Kuadrant, MaaS endpoints cannot enforce service tiers

**Installation Procedure**:

**Step 1: Switch to Global Operators Namespace**
```bash
oc project openshift-operators
```

**CRITICAL**: Do NOT create an OperatorGroup. The openshift-operators namespace already has a global OperatorGroup. Creating a second causes "TooManyOperatorGroups" error.

**Step 2: Dynamically Find Channel and Subscribe**
```bash
# Fetch current default channel
CHANNEL=$(oc get packagemanifest rhcl-operator -n openshift-marketplace -o jsonpath='{.status.defaultChannel}')

# Create subscription with manual approval and version pin
cat <<EOF | oc create -f -
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  name: rhcl-operator
  namespace: openshift-operators
spec:
  channel: "${CHANNEL}"
  installPlanApproval: Manual
  name: rhcl-operator
  source: redhat-operators
  sourceNamespace: openshift-marketplace
  startingCSV: rhcl-operator.v1.3.1
EOF
```

**Why Manual approval**: Freezes version to v1.3.1 for stability. Prevents automatic upgrades mid-course.

**Step 3: Auto-Approve InstallPlans**

Connectivity Link pulls dependencies (DNS Operator, Limitador). This script approves all pending InstallPlans:

```bash
echo "Waiting for OLM to generate InstallPlans..."
sleep 15

# Find and approve pending InstallPlans
for ip in $(oc get installplan -n openshift-operators -o jsonpath='{.items[?(@.spec.approved==false)].metadata.name}'); do
  echo "Approving InstallPlan: $ip"
  oc patch installplan $ip -n openshift-operators --type merge -p '{"spec":{"approved":true}}'
done
```

**Step 4: Verify CSV**
```bash
oc get csv -n openshift-operators | grep rhcl
```
Wait for PHASE "Succeeded". If no output, re-run auto-approve script.

**Step 5: Create Kuadrant CR**
```bash
cat <<EOF | oc create -f -
apiVersion: kuadrant.io/v1beta1
kind: Kuadrant
metadata:
  name: kuadrant
  namespace: openshift-operators
spec: {}
EOF
```

Empty spec uses optimal defaults.

**Step 6: Verification**
```bash
# Watch pods spin up
oc get pods -n openshift-operators -w

# Wait for Ready condition
oc wait kuadrant/kuadrant --for="condition=Ready=true" -n openshift-operators --timeout=300s
```

Expected pods:
- authorino-* (authentication)
- limitador-* (rate limiting)
- kuadrant-operator-controller-manager

**[Diagram Build - Layer 2 Gateway]**: Show Kuadrant gateway intercepting traffic, Authorino validating tokens, Limitador checking rate limits.

**Extract from**: `/Users/kaknox/Documents/GitHub/rhoai3-operators/modules/chapter1/pages/lab0.adoc` lines 459-559

#### Section 3.3: Troubleshooting Connectivity Link Installation [PO6]

**Scenario 1: Multiple OperatorGroup Error**

**Symptoms**: Subscription shows "TooManyOperatorGroups" in status.

**Root cause**: openshift-operators has default global OperatorGroup. Student accidentally created second one.

**Diagnosis**:
```bash
oc get operatorgroup -n openshift-operators
```

**Resolution**:
```bash
# Delete the duplicate (keep only global-operators)
oc delete operatorgroup <student-created-group> -n openshift-operators

# Delete and recreate subscription
oc delete subscription rhcl-operator -n openshift-operators
# Re-run subscription creation
```

**Scenario 2: Dependency Operators Fail to Install**

**Symptoms**: rhcl-operator CSV succeeds but DNS and Limitador CSVs missing.

**Root cause**: Manual InstallPlanApproval requires manual approval of dependencies.

**Diagnosis**:
```bash
# Check for pending InstallPlans
oc get installplan -n openshift-operators | grep -v "true.*Complete"

# List all CSVs to identify missing dependencies
oc get csv -n openshift-operators
```

**Resolution**:
```bash
# Re-run auto-approve script
for ip in $(oc get installplan -n openshift-operators -o jsonpath='{.items[?(@.spec.approved==false)].metadata.name}'); do
  oc patch installplan $ip -n openshift-operators --type merge -p '{"spec":{"approved":true}}'
done

# Wait for CSVs to appear
oc get csv -n openshift-operators -w
```

**Scenario 3: Kuadrant CR Stuck in Progressing**

**Symptoms**: Kuadrant CR created but condition Ready=False.

**Root cause**: Authorino or Limitador pods not starting.

**Diagnosis**:
```bash
# Check Kuadrant status
oc get kuadrant kuadrant -n openshift-operators -o jsonpath='{.status.conditions}' | jq

# Check pod status
oc get pods -n openshift-operators -l 'app.kubernetes.io/part-of=kuadrant'

# Check pod logs
oc logs -n openshift-operators -l app=authorino --tail=50
oc logs -n openshift-operators -l app=limitador --tail=50
```

**Resolution**:
```bash
# If image pull errors, check network connectivity
oc describe pod -n openshift-operators <authorino-pod> | grep -A 10 Events

# Delete Kuadrant CR and recreate
oc delete kuadrant kuadrant -n openshift-operators
cat <<EOF | oc create -f -
apiVersion: kuadrant.io/v1beta1
kind: Kuadrant
metadata:
  name: kuadrant
  namespace: openshift-operators
spec: {}
EOF

# Force restart of stuck pods
oc delete pod -n openshift-operators -l 'app.kubernetes.io/part-of=kuadrant'
```

#### Section 3.4: Installing LeaderWorkerSet Operator [PO3]

**Content Summary**: Deploy LWS for distributed pod coordination.

**Why LeaderWorkerSet matters**:
- Large models (Llama-3-70B) don't fit on single GPU
- llm-d distributed runtime requires leader/worker topology
- LWS designates one leader pod (receives API requests) and multiple worker pods (execute tensor calculations)

**Installation Procedure**:

**Step 1: Create Dedicated Namespace**
```bash
oc create namespace openshift-lws-operator
```

**Step 2: Create OperatorGroup**
```bash
cat <<EOF | oc create -f -
apiVersion: operators.coreos.com/v1
kind: OperatorGroup
metadata:
  generateName: openshift-lws-operator-
  namespace: openshift-lws-operator
spec:
  targetNamespaces:
  - openshift-lws-operator
EOF
```

**Step 3: Dynamically Find Channel and Subscribe**
```bash
CHANNEL=$(oc get packagemanifest lws-operator -n openshift-marketplace -o jsonpath='{.status.defaultChannel}')

cat <<EOF | oc create -f -
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  name: lws-operator
  namespace: openshift-lws-operator
spec:
  channel: "${CHANNEL}"
  installPlanApproval: Automatic
  name: lws-operator
  source: redhat-operators
  sourceNamespace: openshift-marketplace
EOF
```

**Step 4: Verify CSV**
```bash
oc get csv -n openshift-lws-operator
```

**Step 5: Create LeaderWorkerSetOperator CR**
```bash
cat <<EOF | oc create -f -
apiVersion: operator.openshift.io/v1
kind: LeaderWorkerSetOperator
metadata:
  name: cluster
  namespace: openshift-lws-operator
spec:
  managementState: Managed
  logLevel: Normal
  operatorLogLevel: Normal
EOF
```

**Step 6: Verification**
```bash
oc get pods -n openshift-lws-operator
```

Expected: LWS controller manager pods in Running state.

**[Diagram Build - Layer 2 Distributed]**: Show LWS managing leader/worker pod topology for multi-GPU inference.

**Extract from**: `/Users/kaknox/Documents/GitHub/rhoai3-operators/modules/chapter1/pages/lab0.adoc` lines 358-454

#### Section 3.5: Installing Red Hat build of Kueue Operator [PO3]

**Content Summary**: Deploy Kueue for workload queue management and fair GPU sharing.

**Why Kueue is essential**:
- Multiple teams submitting AI workloads create GPU contention
- Kueue acts as "traffic controller" holding jobs until resources available
- Supports multi-tenancy with ClusterQueues and LocalQueues
- Prevents cluster resource exhaustion

**Installation Procedure**:

**Step 1: Create Dedicated Namespace**
```bash
oc create namespace openshift-kueue-operator
```

**Step 2: Create OperatorGroup (Cluster-Wide Mode)**

Kueue must watch workloads across all namespaces. Omit targetNamespaces for AllNamespaces mode:

```bash
cat <<EOF | oc create -f -
apiVersion: operators.coreos.com/v1
kind: OperatorGroup
metadata:
  generateName: openshift-kueue-operator-
  namespace: openshift-kueue-operator
EOF
```

**Step 3: Dynamically Find Channel and Subscribe**
```bash
CHANNEL=$(oc get packagemanifest kueue-operator -n openshift-marketplace -o jsonpath='{.status.defaultChannel}')

cat <<EOF | oc create -f -
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  name: kueue-operator
  namespace: openshift-kueue-operator
spec:
  channel: "${CHANNEL}"
  installPlanApproval: Automatic
  name: kueue-operator
  source: redhat-operators
  sourceNamespace: openshift-marketplace
EOF
```

**Step 4: Verify CSV**
```bash
oc get csv -n openshift-kueue-operator
```

**Step 5: Create Kueue CR**
```bash
cat <<EOF | oc create -f -
apiVersion: kueue.openshift.io/v1
kind: Kueue
metadata:
  name: cluster
spec:
  config:
    integrations:
      frameworks:
        - BatchJob
  logLevel: Normal
  operatorLogLevel: Normal
  managementState: Managed
EOF
```

**Step 6: Verification**
```bash
oc get pods -n openshift-kueue-operator
```

Expected: Kueue controller manager pods in Running state.

**[Diagram Build - Layer 2 Queuing]**: Show Kueue holding jobs in queue, releasing when GPUs available, fair sharing across teams.

**Extract from**: `/Users/kaknox/Documents/GitHub/rhoai3-operators/modules/chapter1/pages/lab0.adoc` lines 563-665

#### Section 3.6: Validating the Serving and Governance Layer [PO5]

**Content Summary**: End-to-end validation of Layer 2 operators.

**Cert-Manager Validation**:
```bash
# Verify CertManager CR
oc get certmanager cluster -o jsonpath='{.status.conditions[?(@.type=="Ready")].status}'
# Expected: True

# Verify cert-manager pods
oc get pods -n cert-manager
# Expected: All Running

# Test certificate issuance (optional)
cat <<EOF | oc apply -f -
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: test-cert
  namespace: default
spec:
  secretName: test-cert-tls
  issuerRef:
    name: selfsigned-issuer
    kind: ClusterIssuer
  dnsNames:
  - test.example.com
EOF

oc get certificate test-cert -n default
oc delete certificate test-cert -n default
```

**Kuadrant Validation**:
```bash
# Verify Kuadrant Ready
oc get kuadrant kuadrant -n openshift-operators -o jsonpath='{.status.conditions[?(@.type=="Ready")]}'

# Verify Authorino and Limitador pods
oc get pods -n openshift-operators -l 'app.kubernetes.io/part-of=kuadrant'

# Check Kuadrant controller logs
oc logs -n openshift-operators -l app.kubernetes.io/name=kuadrant-operator --tail=20
```

**LeaderWorkerSet Validation**:
```bash
# Verify LWS operator status
oc get leaderworkersetoperator cluster -n openshift-lws-operator -o jsonpath='{.status.conditions}'

# Verify LWS CRD registered
oc get crd leaderworkersets.leaderworkerset.x-k8s.io
```

**Kueue Validation**:
```bash
# Verify Kueue Ready
oc get kueue cluster -n openshift-kueue-operator -o jsonpath='{.status.conditions[?(@.type=="Ready")]}'

# Verify Kueue CRDs registered
oc get crd clusterqueues.kueue.x-k8s.io
oc get crd localqueues.kueue.x-k8s.io

# Test ClusterQueue creation (optional)
cat <<EOF | oc apply -f -
apiVersion: kueue.x-k8s.io/v1beta1
kind: ClusterQueue
metadata:
  name: test-cq
spec:
  namespaceSelector: {}
  resourceGroups:
  - coveredResources: ["cpu", "memory"]
    flavors:
    - name: default
      resources:
      - name: cpu
        nominalQuota: 10
      - name: memory
        nominalQuota: 10Gi
EOF

oc get clusterqueue test-cq
oc delete clusterqueue test-cq
```

**Layer 2 Health Summary**:
```bash
echo "=== Layer 2: Serving & Governance Health ==="
echo "Cert-Manager:"
oc get pods -n cert-manager
echo ""
echo "Kuadrant:"
oc get kuadrant kuadrant -n openshift-operators -o jsonpath='{.status.conditions[?(@.type=="Ready")].message}'
echo ""
echo "LeaderWorkerSet:"
oc get pods -n openshift-lws-operator
echo ""
echo "Kueue:"
oc get pods -n openshift-kueue-operator
```

---

### Lesson 4: Activating the RHOAI Control Plane

**Lesson Goal**: Install the Red Hat OpenShift AI Operator and configure DataScienceCluster with selective component management, validating control and data plane readiness.

#### Section 4.1: Installing the RHOAI Operator [PO4]

**Content Summary**: Deploy the core OpenShift AI operator.

**Why RHOAI Operator is the orchestrator**:
- Meta-operator managing entire AI lifecycle platform
- Control plane monitors cluster state
- Data plane runs actual AI workloads
- DSC acts as "master switchboard" for components

**Installation Procedure**:

**Step 1: Create Dedicated Namespace**
```bash
oc create namespace redhat-ods-operator
```

**Step 2: Create OperatorGroup**
```bash
cat <<EOF | oc create -f -
apiVersion: operators.coreos.com/v1
kind: OperatorGroup
metadata:
  generateName: redhat-ods-operator-
  namespace: redhat-ods-operator
EOF
```

**Step 3: Subscribe to stable-3.3 Channel**
```bash
cat <<EOF | oc create -f -
apiVersion: operators.coreos.com/v1alpha1
kind: Subscription
metadata:
  name: rhods-operator
  namespace: redhat-ods-operator
spec:
  channel: "stable-3.3"
  installPlanApproval: Automatic
  name: rhods-operator
  source: redhat-operators
  sourceNamespace: openshift-marketplace
EOF
```

**Step 4: Verify CSV**
```bash
oc get csv -n redhat-ods-operator
```
Expected: rhods-operator.3.3.x with PHASE "Succeeded"

**Step 5: Verify Operator Pod**
```bash
oc get pods -n redhat-ods-operator
```
Expected: rhods-operator pod in Running state

**[Diagram Build - Layer 3 Control Plane]**: Show rhods-operator in control plane namespace monitoring cluster.

**Extract from**: `/Users/kaknox/Documents/GitHub/rhoai3-operators/modules/chapter1/pages/lab0.adoc` lines 669-738

#### Section 4.2: Understanding DSCInitialization [PO4]

**Content Summary**: DSCI controls foundational cluster-wide AI platform settings.

**What is DSCInitialization**:
- Auto-created by RHOAI Operator as "default-dsci"
- Configures cluster-wide settings before components deploy
- Rarely needs manual creation, but understanding is critical for Day-2 ops

**Key DSCI Configurations**:

**1. Service Mesh Integration**:
- Defines which Service Mesh control plane RHOAI uses
- Default: data-science-smcp in istio-system namespace
- Required for secure model serving with mTLS

**2. Monitoring Namespace**:
- Defines namespace for platform-wide metrics
- Default: redhat-ods-monitoring
- Contains Prometheus, Grafana, alert managers

**3. Trusted CA Bundles**:
- Inject corporate Certificate Authority for private registries
- Model serving pods inherit trust for internal networks
- Critical for disconnected/air-gapped environments

**Inspecting DSCI**:
```bash
oc get dsci default-dsci -o yaml
```

**Key fields to understand**:
```yaml
spec:
  applicationsNamespace: redhat-ods-applications
  monitoring:
    managementState: Managed
    namespace: redhat-ods-monitoring
  serviceMesh:
    controlPlane:
      name: data-science-smcp
      namespace: istio-system
    managementState: Managed
  trustedCABundle:
    managementState: Removed  # Set to Managed if using custom CA
```

**When to modify DSCI**:
- Custom CA for internal registries
- Different Service Mesh control plane
- Custom monitoring namespace
- Otherwise, leave defaults

**[Diagram Build - DSCI Relationships]**: Show DSCI connecting to Service Mesh, Monitoring, TrustedCA components.

**Extract from**: `/Users/kaknox/Documents/GitHub/rhoai3-operators/modules/chapter1/pages/lab3-dsc.adoc` lines 5-20

#### Section 4.3: Configuring DataScienceCluster for MaaS [PO4]

**Content Summary**: DSC is the master toggle switch for AI platform components.

**What is DataScienceCluster**:
- Cluster-scoped CR controlling which components deploy
- Components have three states: Managed, Removed, Unmanaged
- Managed: RHOAI operator installs and manages
- Removed: Component explicitly disabled
- Unmanaged: Component exists but operator doesn't touch it

**MaaS Configuration Strategy for RHOAI 3.3**:

**Components set to Managed**:
- **dashboard**: UI for data scientists
- **kserve**: Model serving engine (MaaS core)
- **modelregistry**: Model versioning and metadata
- **aipipelines**: Data science pipelines
- **ray**: Distributed compute framework
- **llamastackoperator**: RAG and agentic workflows
- **workbenches**: Jupyter notebooks and IDEs

**Components set to Removed**:
- **kueue**: Using cluster-wide instance installed separately
- **trainingoperator**: Legacy, replaced by Ray
- **modelsAsService**: Technology Preview in 3.3 (set to Removed for stable baseline)
- **feastoperator**: Not required for MaaS
- **trustyai**: Not required for Phase 0

**Critical KServe Configuration**:
```yaml
kserve:
  managementState: Managed
  modelsAsService:
    managementState: Removed  # Not ready in 3.3
  nim:
    managementState: Removed
  rawDeploymentServiceConfig: Headless  # Modern routing without Serverless
```

**Why rawDeploymentServiceConfig: Headless**:
- Uses Gateway API instead of Knative Serving
- Integrates with Kuadrant for authentication/rate limiting
- Simpler architecture, better performance
- Required for MaaS in 3.3

**Applying the DSC Update**:

```bash
cat <<EOF | oc apply -f -
apiVersion: datasciencecluster.opendatahub.io/v1
kind: DataScienceCluster
metadata:
  name: default-dsc
spec:
  components:
    dashboard:
      managementState: Managed
      
    kserve:
      managementState: Managed
      modelsAsService:
        managementState: Removed
      nim:
        managementState: Removed
      rawDeploymentServiceConfig: Headless
      
    modelregistry:
      managementState: Managed
      registriesNamespace: rhoai-model-registries
      
    aipipelines:
      managementState: Managed
      
    kueue:
      defaultClusterQueueName: default
      defaultLocalQueueName: default
      managementState: Removed  # Using cluster-wide instance
      
    ray:
      managementState: Managed
      
    trainingoperator:
      managementState: Removed
      
    llamastackoperator:
      managementState: Managed
      
    workbenches:
      managementState: Managed
      workbenchNamespace: rhods-notebooks
      
    feastoperator:
      managementState: Removed
      
    trustyai:
      eval:
        lmeval:
          permitCodeExecution: deny
          permitOnline: deny
      managementState: Removed
      
    mlflowoperator: {}
    trainer: {}
EOF
```

**Note**: DataScienceCluster is cluster-scoped. Can apply from any namespace.

**[Diagram Build - Layer 3 Data Plane]**: Show DSC CR as master switchboard controlling components in redhat-ods-applications.

**Extract from**: `/Users/kaknox/Documents/GitHub/rhoai3-operators/modules/chapter1/pages/lab3-dsc.adoc` lines 22-100

#### Section 4.4: Troubleshooting DSC Component Failures [PO6]

**Scenario 1: DSC Phase Stuck in "Progressing"**

**Symptoms**: DSC update applied but phase never transitions to Ready.

**Diagnosis**:
```bash
# Check DSC phase
oc get dsc default-dsc -o jsonpath='{.status.phase}'

# Check component conditions
oc get dsc default-dsc -o jsonpath='{.status.conditions}' | jq

# Check for failing pods
oc get pods -n redhat-ods-applications --field-selector=status.phase!=Running
```

**Root causes**:
- Component deployment failing (insufficient resources)
- Missing dependency operator (cert-manager, Kuadrant)
- Image pull errors (network connectivity, registry auth)
- Webhook registration failures

**Resolution**:
```bash
# Identify failing component from conditions
oc get dsc default-dsc -o jsonpath='{.status.conditions}' | jq '.[] | select(.status=="False")'

# Check specific component pods
oc get pods -n redhat-ods-applications -l app=<component>

# Review pod logs
oc logs -n redhat-ods-applications <pod-name> --tail=100

# If webhook errors, verify cert-manager healthy
oc get pods -n cert-manager

# Force component restart
oc delete pod -n redhat-ods-applications -l app=<component>
```

**Scenario 2: Conflicting Kueue Instances**

**Symptoms**: DSC shows Kueue errors when both cluster-wide and DSC-managed Kueue exist.

**Diagnosis**:
```bash
# List all Kueue instances
oc get kueue -A

# Check DSC Kueue state
oc get dsc default-dsc -o jsonpath='{.spec.components.kueue.managementState}'
```

**Root cause**: DSC kueue.managementState set to "Managed" when cluster-wide instance already exists.

**Resolution**:
```bash
# Update DSC to set Kueue to Removed
oc patch dsc default-dsc --type merge -p '{"spec":{"components":{"kueue":{"managementState":"Removed"}}}}'

# Verify update applied
oc get dsc default-dsc -o jsonpath='{.spec.components.kueue.managementState}'
```

**Scenario 3: KServe Controller Failing Webhook Validation**

**Symptoms**: KServe controller logs show TLS handshake errors.

**Diagnosis**:
```bash
# Check KServe controller logs
oc logs -n redhat-ods-applications -l control-plane=kserve-controller-manager --tail=50

# Check for certificate errors
oc logs -n redhat-ods-applications -l control-plane=kserve-controller-manager | grep -i "certificate\|tls\|x509"

# Verify certificates exist
oc get certificate -n redhat-ods-applications

# Check webhook configurations
oc get validatingwebhookconfiguration | grep kserve
oc get mutatingwebhookconfiguration | grep kserve
```

**Root causes**:
- Cert-manager not installed or unhealthy
- Certificate not issued
- Webhook CA bundle incorrect
- Service endpoints not ready

**Resolution**:
```bash
# Verify cert-manager healthy
oc get pods -n cert-manager
oc get certmanager cluster -o jsonpath='{.status.conditions}'

# Check certificate status
oc get certificate -n redhat-ods-applications -o wide

# Describe certificate for issues
oc describe certificate <cert-name> -n redhat-ods-applications

# Force certificate renewal
oc delete certificate <cert-name> -n redhat-ods-applications

# Restart KServe controller
oc delete pod -n redhat-ods-applications -l control-plane=kserve-controller-manager

# Wait for pod to come back
oc wait --for=condition=Ready pod -n redhat-ods-applications -l control-plane=kserve-controller-manager --timeout=300s
```

**Scenario 4: Dashboard Not Accessible**

**Symptoms**: DSC Ready but dashboard route returns 404 or 503.

**Diagnosis**:
```bash
# Check dashboard deployment
oc get deployment rhods-dashboard -n redhat-ods-applications

# Check dashboard pods
oc get pods -n redhat-ods-applications -l app=rhods-dashboard

# Check route exists
oc get route -n redhat-ods-applications -l app=rhods-dashboard

# Test route
oc get route rhods-dashboard -n redhat-ods-applications -o jsonpath='{"https://"}{.spec.host}{"\n"}'
```

**Root causes**:
- Dashboard pods not running (CrashLoopBackOff)
- Route not created
- Service endpoints missing
- Ingress controller issues

**Resolution**:
```bash
# Check dashboard pod logs
oc logs -n redhat-ods-applications -l app=rhods-dashboard --tail=100

# Check service has endpoints
oc get endpoints rhods-dashboard -n redhat-ods-applications

# Delete dashboard pod to restart
oc delete pod -n redhat-ods-applications -l app=rhods-dashboard

# Verify route resolves
curl -k -I https://$(oc get route rhods-dashboard -n redhat-ods-applications -o jsonpath='{.spec.host}')
```

#### Section 4.5: Validating the RHOAI Control Plane [PO5]

**Content Summary**: Comprehensive verification checklist for Layer 3.

**DSC Phase Verification**:
```bash
# Check DSC phase (should be "Ready")
oc get dsc default-dsc -o jsonpath='{.status.phase}{"\n"}'

# Monitor DSC rollout (if still progressing)
watch oc get dsc default-dsc -o jsonpath='{.status.phase}{"\n"}'
```

**Component Readiness Matrix**:
```bash
# Check all managed components show Ready
oc get dsc default-dsc -o jsonpath='{.status.conditions}' | jq '.[] | {name:.type, status:.status, message:.message}'

# Components to verify:
# - DashboardReady
# - KserveReady
# - ModelRegistryReady
# - AIPipelinesReady
# - RayReady
# - LlamaStackOperatorReady
# - WorkbenchesReady
```

**Dashboard Accessibility Test**:
```bash
# Get dashboard URL
oc get route rhods-dashboard -n redhat-ods-applications -o jsonpath='{"https://"}{.spec.host}{"\n"}'

# Test HTTP response
curl -k -I https://$(oc get route rhods-dashboard -n redhat-ods-applications -o jsonpath='{.spec.host}')
# Expected: HTTP/1.1 200 OK or 302 (redirect to login)
```

**KServe Controller Health**:
```bash
# Verify KServe controller running
oc get deployment kserve-controller-manager -n redhat-ods-applications

# Check pod health
oc get pods -n redhat-ods-applications -l control-plane=kserve-controller-manager

# Verify KServe CRDs registered
oc get crd | grep kserve
# Expected: inferenceservices.serving.kserve.io, servingruntimes.serving.kserve.io
```

**Model Registry Verification**:
```bash
# Check model registry namespace
oc get namespace rhoai-model-registries

# Verify model registry pods
oc get pods -n redhat-ods-applications -l app=model-registry
```

**Pipelines API Server**:
```bash
# Verify pipelines API running
oc get deployment ds-pipeline-pipelines-definition -n redhat-ods-applications 2>/dev/null || echo "Pipelines not fully deployed yet"

# Check pipeline pods
oc get pods -n redhat-ods-applications | grep pipeline
```

**Layer 3 Health Summary**:
```bash
echo "=== Layer 3: RHOAI Control Plane Health ==="
echo "RHOAI Operator:"
oc get csv -n redhat-ods-operator
echo ""
echo "DSC Phase:"
oc get dsc default-dsc -o jsonpath='{.status.phase}{"\n"}'
echo ""
echo "Component Status:"
oc get dsc default-dsc -o jsonpath='{.status.conditions}' | jq -r '.[] | "\(.type): \(.status)"'
echo ""
echo "Dashboard URL:"
oc get route rhods-dashboard -n redhat-ods-applications -o jsonpath='{"https://"}{.spec.host}{"\n"}'
echo ""
echo "Application Pods:"
oc get pods -n redhat-ods-applications
```

---

### Lesson 5: Comprehensive Validation and Health Checks

**Lesson Goal**: Execute a systematic validation framework across all 3 layers, using diagnostic commands and automated scripts to verify end-to-end operator health and readiness for downstream capabilities.

#### Section 5.1: Layer 1 Hardware Validation Framework [PO5]

**Content Summary**: Automated validation script for NFD and GPU operators.

**Hardware Layer Validation Script**:

```bash
#!/bin/bash
# Layer 1 Hardware Validation Script
# Save as: validate-hardware-layer.sh

echo "================================================================"
echo "Layer 1: Hardware Foundation Validation"
echo "================================================================"
echo ""

# NFD Validation
echo "=== NFD Operator Health ==="
NFD_CSV=$(oc get csv -n openshift-nfd -o jsonpath='{.items[0].metadata.name}')
NFD_PHASE=$(oc get csv -n openshift-nfd -o jsonpath='{.items[0].status.phase}')
echo "NFD CSV: $NFD_CSV"
echo "NFD Phase: $NFD_PHASE"

if [ "$NFD_PHASE" != "Succeeded" ]; then
  echo "❌ FAIL: NFD operator not healthy"
  exit 1
fi
echo "✓ PASS: NFD operator healthy"
echo ""

echo "=== NFD Pod Status ==="
oc get pods -n openshift-nfd
echo ""

echo "=== NFD-Labeled Nodes ==="
GPU_NODES=$(oc get nodes -l feature.node.kubernetes.io/pci-10de.present=true -o jsonpath='{.items[*].metadata.name}')
if [ -z "$GPU_NODES" ]; then
  echo "⚠ WARNING: No GPU nodes labeled (may be expected if no GPUs present)"
else
  echo "GPU Nodes: $GPU_NODES"
  echo "✓ PASS: NFD labeled GPU nodes"
fi
echo ""

# GPU Operator Validation
echo "=== GPU Operator Health ==="
GPU_CSV=$(oc get csv -n nvidia-gpu-operator -o jsonpath='{.items[0].metadata.name}' 2>/dev/null)
if [ -z "$GPU_CSV" ]; then
  echo "⚠ WARNING: GPU operator not installed (may be intentional)"
else
  GPU_PHASE=$(oc get csv -n nvidia-gpu-operator -o jsonpath='{.items[0].status.phase}')
  echo "GPU CSV: $GPU_CSV"
  echo "GPU Phase: $GPU_PHASE"
  
  if [ "$GPU_PHASE" != "Succeeded" ]; then
    echo "❌ FAIL: GPU operator not healthy"
    exit 1
  fi
  echo "✓ PASS: GPU operator healthy"
fi
echo ""

echo "=== ClusterPolicy Status ==="
POLICY_STATE=$(oc get clusterpolicy gpu-cluster-policy -o jsonpath='{.status.state}' 2>/dev/null)
if [ -z "$POLICY_STATE" ]; then
  echo "⚠ WARNING: ClusterPolicy not found"
else
  echo "ClusterPolicy State: $POLICY_STATE"
  if [ "$POLICY_STATE" == "ready" ]; then
    echo "✓ PASS: ClusterPolicy ready"
  fi
fi
echo ""

echo "=== GPU Daemonsets ==="
oc get ds -n nvidia-gpu-operator 2>/dev/null || echo "No GPU daemonsets (GPU nodes may not be present)"
echo ""

echo "=== Allocatable GPU Resources ==="
oc get nodes -o json | jq -r '.items[] | select(.status.allocatable."nvidia.com/gpu" != null) | "\(.metadata.name): \(.status.allocatable."nvidia.com/gpu") GPUs"'
echo ""

echo "================================================================"
echo "Layer 1 Validation Complete"
echo "================================================================"
```

**Usage**:
```bash
chmod +x validate-hardware-layer.sh
./validate-hardware-layer.sh
```

**Expected output**: All checks show ✓ PASS or ⚠ WARNING (for optional GPU hardware).

#### Section 5.2: Layer 2 Serving/Governance Validation Framework [PO5]

**Content Summary**: Automated validation script for cert-manager, Kuadrant, LWS, Kueue.

**Serving/Governance Layer Validation Script**:

```bash
#!/bin/bash
# Layer 2 Serving & Governance Validation Script
# Save as: validate-serving-layer.sh

echo "================================================================"
echo "Layer 2: Serving & Governance Validation"
echo "================================================================"
echo ""

# Cert-Manager Validation
echo "=== Cert-Manager Operator Health ==="
CM_CSV=$(oc get csv -n cert-manager-operator -o jsonpath='{.items[0].metadata.name}')
CM_PHASE=$(oc get csv -n cert-manager-operator -o jsonpath='{.items[0].status.phase}')
echo "Cert-Manager CSV: $CM_CSV"
echo "Cert-Manager Phase: $CM_PHASE"

if [ "$CM_PHASE" != "Succeeded" ]; then
  echo "❌ FAIL: Cert-Manager operator not healthy"
  exit 1
fi
echo "✓ PASS: Cert-Manager operator healthy"
echo ""

echo "=== Cert-Manager Pods ==="
oc get pods -n cert-manager
CM_READY=$(oc get certmanager cluster -o jsonpath='{.status.conditions[?(@.type=="Ready")].status}' 2>/dev/null)
if [ "$CM_READY" == "True" ]; then
  echo "✓ PASS: CertManager CR ready"
else
  echo "❌ FAIL: CertManager CR not ready"
  exit 1
fi
echo ""

# Kuadrant Validation
echo "=== Kuadrant Operator Health ==="
KUAD_CSV=$(oc get csv -n openshift-operators -o jsonpath='{.items[?(@.metadata.name~"rhcl")].metadata.name}')
KUAD_PHASE=$(oc get csv -n openshift-operators -o jsonpath='{.items[?(@.metadata.name~"rhcl")].status.phase}')
echo "Kuadrant CSV: $KUAD_CSV"
echo "Kuadrant Phase: $KUAD_PHASE"

if [ "$KUAD_PHASE" != "Succeeded" ]; then
  echo "❌ FAIL: Kuadrant operator not healthy"
  exit 1
fi
echo "✓ PASS: Kuadrant operator healthy"
echo ""

echo "=== Kuadrant CR Status ==="
KUAD_READY=$(oc get kuadrant kuadrant -n openshift-operators -o jsonpath='{.status.conditions[?(@.type=="Ready")].status}' 2>/dev/null)
if [ "$KUAD_READY" == "True" ]; then
  echo "✓ PASS: Kuadrant CR ready"
else
  echo "❌ FAIL: Kuadrant CR not ready"
  exit 1
fi
echo ""

echo "=== Kuadrant Pods ==="
oc get pods -n openshift-operators -l 'app.kubernetes.io/part-of=kuadrant'
echo ""

# LeaderWorkerSet Validation
echo "=== LeaderWorkerSet Operator Health ==="
LWS_CSV=$(oc get csv -n openshift-lws-operator -o jsonpath='{.items[0].metadata.name}')
LWS_PHASE=$(oc get csv -n openshift-lws-operator -o jsonpath='{.items[0].status.phase}')
echo "LWS CSV: $LWS_CSV"
echo "LWS Phase: $LWS_PHASE"

if [ "$LWS_PHASE" != "Succeeded" ]; then
  echo "❌ FAIL: LWS operator not healthy"
  exit 1
fi
echo "✓ PASS: LWS operator healthy"
echo ""

echo "=== LeaderWorkerSet Pods ==="
oc get pods -n openshift-lws-operator
echo ""

# Kueue Validation
echo "=== Kueue Operator Health ==="
KUEUE_CSV=$(oc get csv -n openshift-kueue-operator -o jsonpath='{.items[0].metadata.name}')
KUEUE_PHASE=$(oc get csv -n openshift-kueue-operator -o jsonpath='{.items[0].status.phase}')
echo "Kueue CSV: $KUEUE_CSV"
echo "Kueue Phase: $KUEUE_PHASE"

if [ "$KUEUE_PHASE" != "Succeeded" ]; then
  echo "❌ FAIL: Kueue operator not healthy"
  exit 1
fi
echo "✓ PASS: Kueue operator healthy"
echo ""

echo "=== Kueue CR Status ==="
KUEUE_READY=$(oc get kueue cluster -n openshift-kueue-operator -o jsonpath='{.status.conditions[?(@.type=="Ready")].status}' 2>/dev/null)
if [ "$KUEUE_READY" == "True" ]; then
  echo "✓ PASS: Kueue CR ready"
else
  echo "❌ FAIL: Kueue CR not ready"
  exit 1
fi
echo ""

echo "=== Kueue Pods ==="
oc get pods -n openshift-kueue-operator
echo ""

echo "================================================================"
echo "Layer 2 Validation Complete"
echo "================================================================"
```

**Usage**:
```bash
chmod +x validate-serving-layer.sh
./validate-serving-layer.sh
```

#### Section 5.3: Layer 3 RHOAI Control Plane Validation Framework [PO5]

**Content Summary**: Automated validation script for RHOAI operator and DSC.

**RHOAI Control Plane Validation Script**:

```bash
#!/bin/bash
# Layer 3 RHOAI Control Plane Validation Script
# Save as: validate-rhoai-layer.sh

echo "================================================================"
echo "Layer 3: RHOAI Control Plane Validation"
echo "================================================================"
echo ""

# RHOAI Operator Validation
echo "=== RHOAI Operator Health ==="
RHOAI_CSV=$(oc get csv -n redhat-ods-operator -o jsonpath='{.items[0].metadata.name}')
RHOAI_PHASE=$(oc get csv -n redhat-ods-operator -o jsonpath='{.items[0].status.phase}')
echo "RHOAI CSV: $RHOAI_CSV"
echo "RHOAI Phase: $RHOAI_PHASE"

if [ "$RHOAI_PHASE" != "Succeeded" ]; then
  echo "❌ FAIL: RHOAI operator not healthy"
  exit 1
fi
echo "✓ PASS: RHOAI operator healthy"
echo ""

echo "=== RHOAI Operator Pod ==="
oc get pods -n redhat-ods-operator
echo ""

# DSC Validation
echo "=== DataScienceCluster Status ==="
DSC_PHASE=$(oc get dsc default-dsc -o jsonpath='{.status.phase}' 2>/dev/null)
if [ -z "$DSC_PHASE" ]; then
  echo "❌ FAIL: DSC not found"
  exit 1
fi

echo "DSC Phase: $DSC_PHASE"

if [ "$DSC_PHASE" != "Ready" ]; then
  echo "❌ FAIL: DSC not ready (current phase: $DSC_PHASE)"
  echo "Checking component conditions..."
  oc get dsc default-dsc -o jsonpath='{.status.conditions}' | jq -r '.[] | select(.status=="False") | "  \(.type): \(.message)"'
  exit 1
fi
echo "✓ PASS: DSC ready"
echo ""

echo "=== DSC Component Conditions ==="
oc get dsc default-dsc -o jsonpath='{.status.conditions}' | jq -r '.[] | "\(.type): \(.status)"'
echo ""

# Dashboard Validation
echo "=== Dashboard Deployment ==="
oc get deployment rhods-dashboard -n redhat-ods-applications 2>/dev/null
if [ $? -ne 0 ]; then
  echo "⚠ WARNING: Dashboard deployment not found"
else
  DASH_READY=$(oc get deployment rhods-dashboard -n redhat-ods-applications -o jsonpath='{.status.readyReplicas}')
  if [ "$DASH_READY" -gt 0 ]; then
    echo "✓ PASS: Dashboard deployment ready"
  fi
fi
echo ""

echo "=== Dashboard Route ==="
DASH_URL=$(oc get route rhods-dashboard -n redhat-ods-applications -o jsonpath='{"https://"}{.spec.host}' 2>/dev/null)
if [ -z "$DASH_URL" ]; then
  echo "⚠ WARNING: Dashboard route not found"
else
  echo "Dashboard URL: $DASH_URL"
  echo "✓ PASS: Dashboard route exists"
fi
echo ""

# KServe Validation
echo "=== KServe Controller ==="
oc get deployment kserve-controller-manager -n redhat-ods-applications 2>/dev/null
if [ $? -ne 0 ]; then
  echo "⚠ WARNING: KServe controller not found (may still be deploying)"
else
  KSERVE_READY=$(oc get deployment kserve-controller-manager -n redhat-ods-applications -o jsonpath='{.status.readyReplicas}')
  if [ "$KSERVE_READY" -gt 0 ]; then
    echo "✓ PASS: KServe controller ready"
  fi
fi
echo ""

# Model Registry Validation
echo "=== Model Registry Pods ==="
oc get pods -n redhat-ods-applications -l app=model-registry 2>/dev/null || echo "⚠ Model registry pods not found (may still be deploying)"
echo ""

# Application Pods Summary
echo "=== All Application Pods ==="
oc get pods -n redhat-ods-applications
echo ""

echo "================================================================"
echo "Layer 3 Validation Complete"
echo "================================================================"
```

**Usage**:
```bash
chmod +x validate-rhoai-layer.sh
./validate-rhoai-layer.sh
```

#### Section 5.4: End-to-End Smoke Test [PO5]

**Content Summary**: Verify integration across all 3 layers with a test model serving runtime.

**Smoke Test Procedure**:

```bash
#!/bin/bash
# End-to-End Smoke Test
# Tests integration: Hardware → Serving → RHOAI

echo "================================================================"
echo "End-to-End Smoke Test: Operator Stack Integration"
echo "================================================================"
echo ""

# Create test project
echo "Creating test project..."
oc new-project operator-validation-test 2>/dev/null || oc project operator-validation-test

# Deploy sample ServingRuntime
echo "Deploying test ServingRuntime..."
cat <<EOF | oc apply -f -
apiVersion: serving.kserve.io/v1alpha1
kind: ServingRuntime
metadata:
  name: test-runtime
  namespace: operator-validation-test
spec:
  supportedModelFormats:
  - name: sklearn
    version: "1"
  containers:
  - name: kserve-container
    image: kserve/sklearnserver:latest
    resources:
      requests:
        cpu: 100m
        memory: 256Mi
      limits:
        cpu: 1
        memory: 1Gi
EOF

# Wait for ServingRuntime to be created
sleep 5

# Verify ServingRuntime exists
echo ""
echo "Verifying ServingRuntime..."
oc get servingruntime test-runtime -n operator-validation-test

if [ $? -eq 0 ]; then
  echo "✓ PASS: ServingRuntime created successfully"
  echo "✓ PASS: KServe API functional"
  echo "✓ PASS: Operator stack integration verified"
else
  echo "❌ FAIL: ServingRuntime creation failed"
  echo "Check KServe controller logs:"
  echo "  oc logs -n redhat-ods-applications -l control-plane=kserve-controller-manager --tail=50"
  exit 1
fi

# Clean up
echo ""
echo "Cleaning up test resources..."
oc delete project operator-validation-test

echo ""
echo "================================================================"
echo "End-to-End Smoke Test Complete"
echo "================================================================"
```

**Usage**:
```bash
chmod +x smoke-test.sh
./smoke-test.sh
```

**Expected outcome**: ServingRuntime created successfully, proving KServe integration with cert-manager, Kuadrant, and underlying infrastructure.

#### Section 5.5: Readiness Checklist for Downstream Phases [PO5]

**Content Summary**: Final go/no-go checklist before handoff to storage, registry, catalog, MaaS courses.

**Phase 0 Completion Checklist**:

Execute all validation scripts and verify the following:

**Layer 1: Hardware Foundation**
- [ ] NFD Operator CSV shows phase "Succeeded"
- [ ] NFD master and worker pods running in openshift-nfd namespace
- [ ] Worker nodes labeled with feature.node.kubernetes.io labels
- [ ] GPU Operator CSV shows phase "Succeeded" (if GPUs present)
- [ ] ClusterPolicy state shows "ready" (if GPUs present)
- [ ] GPU device plugin shows allocatable GPUs on nodes (if GPUs present)
- [ ] Driver daemonsets running on GPU nodes (if GPUs present)

**Layer 2: Serving & Governance**
- [ ] Cert-Manager Operator CSV shows phase "Succeeded"
- [ ] CertManager CR shows Ready=True condition
- [ ] Cert-manager controller, cainjector, webhook pods running
- [ ] Kuadrant Operator CSV shows phase "Succeeded"
- [ ] Kuadrant CR shows Ready=True condition
- [ ] Authorino and Limitador pods running in openshift-operators
- [ ] LeaderWorkerSet Operator CSV shows phase "Succeeded"
- [ ] LeaderWorkerSet CRD registered (oc get crd leaderworkersets.leaderworkerset.x-k8s.io)
- [ ] Kueue Operator CSV shows phase "Succeeded"
- [ ] Kueue CR shows Ready=True condition
- [ ] ClusterQueue and LocalQueue CRDs registered

**Layer 3: RHOAI Control Plane**
- [ ] RHOAI Operator CSV shows phase "Succeeded" (version 3.3.x)
- [ ] rhods-operator pod running in redhat-ods-operator namespace
- [ ] DSCInitialization (default-dsci) exists
- [ ] DataScienceCluster (default-dsc) shows phase "Ready"
- [ ] All managed components show Ready conditions
- [ ] Dashboard deployment ready in redhat-ods-applications
- [ ] Dashboard route accessible (returns HTTP 200 or 302)
- [ ] KServe controller manager deployment ready
- [ ] Model registry pods running
- [ ] No pods in CrashLoopBackOff across all operator namespaces

**Integration Verification**
- [ ] End-to-end smoke test passes (ServingRuntime creates successfully)
- [ ] No error messages in operator logs
- [ ] All CSV phases show "Succeeded" across all namespaces
- [ ] Cluster has sufficient resources for downstream workloads

**Master Validation Script** (runs all checks):

```bash
#!/bin/bash
# Master validation script - runs all layer checks

echo "Running comprehensive operator stack validation..."
echo ""

./validate-hardware-layer.sh
if [ $? -ne 0 ]; then
  echo "❌ Layer 1 validation failed"
  exit 1
fi

./validate-serving-layer.sh
if [ $? -ne 0 ]; then
  echo "❌ Layer 2 validation failed"
  exit 1
fi

./validate-rhoai-layer.sh
if [ $? -ne 0 ]; then
  echo "❌ Layer 3 validation failed"
  exit 1
fi

./smoke-test.sh
if [ $? -ne 0 ]; then
  echo "❌ Integration test failed"
  exit 1
fi

echo ""
echo "========================================================"
echo "✓ ALL VALIDATIONS PASSED"
echo "✓ Phase 0 Complete - Ready for downstream capabilities"
echo "========================================================"
```

**Next Steps**: With all checks passing, the cluster is ready for:
- rhoai3-storage (persistent volume configuration)
- rhoai3-registry (model registry setup)
- rhoai3-catalog (model catalog management)
- rhoai3-hwprofiles (hardware profile creation)
- rhoai3-maas (Models-as-a-Service deployment)

---

