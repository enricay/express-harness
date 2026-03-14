

Push/PR to master
      │
      ├── build job
      │     ├── Notify Harness STARTED
      │     ├── Build multi-arch image (amd64 + arm64)
      │     └── Push to DockerHub with short SHA tag
      │
      └── deploy job (push only, skipped on PR)
            ├── Update Helm values.yaml via yq
            ├── Commit + push to helm repo
            ├── ArgoCD login + sync
            ├── ArgoCD health wait (300s timeout)
            │
            ├── HEALTHY path
            │     └── Report DORA Deployment — Success
            │
            └── DEGRADED path
                  ├── Report DORA Deployment — Failed
                  ├── Report Incident OPEN (CFR)
                  ├── ArgoCD rollback
                  ├── Health wait post-rollback
                  └── Report Incident Resolved/Open (MTTR)


# Next
Security scanning job
      ├── Trivy    — container image CVEs
      ├── Snyk     — dependency vulnerabilities
      ├── Semgrep  — static code analysis (SAST)
      └── OWASP ZAP — live app scanning (DAST)
            │
            └── All results → DefectDojo
                      │
                      └── Critical findings count → Harness
                                (DORA Security posture metric)



# DORA Coverage


MetricEndpointSignalDeployment Frequency/ng/api/events/deploymentevery sync completionLead Time for Changes/ng/api/events/deploymentstartedAt + endedAt + commitShaChange Failure Rate/cv/api/change-event/registerstatus: Open on DEGRADEDMTTR/cv/api/change-event/registerstatus: Resolved + mttrSeconds

# Secrets required
SecretUsed forDOCKERHUB_USERNAMEimage name + loginDOCKERHUB_TOKENDockerHub authGH_TOKENpush to helm repoHARNESS_API_KEYall Harness API callsHARNESS_ACCOUNT_IDHarness identifiersHARNESS_ORG_IDHarness identifiersHARNESS_PROJECT_IDHarness identifiersARGOCD_SERVERArgoCD loginARGOCD_USERNAMEArgoCD loginARGOCD_PASSWORDArgoCD loginTARGET_APPArgoCD app nameTEAMS_WEBHOOKTeams notification (commented out, ready)