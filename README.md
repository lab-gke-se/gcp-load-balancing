## Repo for provisioning GCP Application Load balancers using K8s Gateway API

### Follow the Google Documentation
https://cloud.google.com/kubernetes-engine/docs/how-to/deploying-gateways




export namespace=app-team
export KSA=external-dns
export svcEmail=external-dns-sa@lab-gke-se.iam.gserviceaccount.com
export project_id=lab-gke-se

# add these roles to GSA
roles="roles/container.clusterViewer roles/container.viewer roles/iam.workloadIdentityUser roles/dns.admin roles/dns.reader"
for role in $roles; do
  gcloud projects add-iam-policy-binding lab-gke-se --member=serviceAccount:$svcEmail --role=$role > /dev/null
done


echo "show final '$svcEmail' roles"
gcloud projects get-iam-policy $project_id --flatten="bindings[].members" --filter="bindings.members=serviceAccount:$svcEmail" --format="value(bindings.role)"

# annotate KSA


# bind GSA and KSA
gcloud iam service-accounts add-iam-policy-binding $svcEmail --role roles/iam.workloadIdentityUser --member serviceAccount:${project_id}.svc.id.goog[$namespace/$KSA]

