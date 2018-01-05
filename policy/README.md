* Create namespace "ns1"

kubectl create -f ~/policy/ns1.yaml

# Should create an empty address_set for that namespace

kubectl delete -f ~/policy/ns1.yaml
  
# Should delete the address set

kubectl create -f ~/policy/ns1.yaml

# Should create it again

* Create policy

kubectl create -f ~/policy/policy1.yaml

  # For each ingress rule, create an adderss_set for each pod selector
  
kubectl delete -f ~/policy/policy1.yaml

# For each ingress rule, delete the adderss_set for each pod selector

kubectl create -f ~/policy/policy1.yaml

* Create 2 pods to which policy needs to be applied.

kubectl create -f ~/policy/policypod1.yaml
kubectl create -f ~/policy/policypod2.yaml

  # The the pod IP addresses should populate the namespace address set.

  # For each pod, ACLs should be added to the logical switch in question.
  # You should have allow-from from an ipv4.src address_set to a specific tcp4.dst
  #  and udp4.dst ports.
  # outport in each ACL should be set to the 2 pods that we put up.
    
  # ovn-nbctl list acl | grep match | grep policypod1

  # The pods should have a default deny ACL
  # ovn-nbctl list acl | grep 'action\|match'

# Delete the above pods to see the ACLs getting deleted

kubectl delete -f ~/policy/policypod1.yaml

  # ovn-nbctl list acl | grep pod1
  
kubectl delete -f ~/policy/policypod2.yaml

  # ovn-nbctl list acl | grep pod2


# Re-create the pods

kubectl create -f ~/policy/policypod1.yaml
kubectl create -f ~/policy/policypod2.yaml

  # You should not be able to curl $IP:80 for these pods from master.

# Create 2 pods with labels
#  role: frontend1

kubectl create -f ~/policy/testpod1.yaml
kubectl create -f ~/policy/testpod2.yaml

  # The address_set "ns1.policy1.ingress.0" should be updated with 2
  #  IP addresses.

* Create 2 pods with labels
  role: frontend2
kubectl create -f ~/policy/testpod3.yaml
kubectl create -f ~/policy/testpod4.yaml

  # The address_set "ns1.policy1.ingress.1" should be updated with 2 IP
  #  addresses.

* Delete the 2 pods with labels "role: frontend1"

kubectl delete -f ~/policy/testpod1.yaml
kubectl delete -f ~/policy/testpod2.yaml

   # The address_set "guru#test-network-policy#ingress#0" should be updated to have no
   # more IP addresses.

kubectl delete -f ~/policy/testpod3.yaml
kubectl delete -f ~/policy/testpod4.yaml

* Create 2 pods with labels
  role: frontend1

kubectl create -f ~/policy/testpod1.yaml
kubectl create -f ~/policy/testpod2.yaml

  # The address_set "guru#test-network-policy#ingress#0" should be updated with 2
  # IP addresses.

* Create 2 pods with labels
  role: frontend2

kubectl create -f ~/policy/testpod3.yaml
kubectl create -f ~/policy/testpod4.yaml

  # The address_set "guru#test-network-policy#ingress#1" should be updated with 2 IP
  # addresses.


Namespace Selectors
===================

* Create new namespaces

kubectl create -f ~/policy/namespaceproject1.yaml
kubectl create -f ~/policy/namespaceproject1twin.yaml
kubectl create -f ~/policy/namespaceproject2.yaml

   # 3 new address_set "project1" and "project1twin" and "project2" is created.
   # The ACLs (for all pods in ns1 selected by policy1) are updated with the address_sets
   # ovn-nbctl list acl | grep ip4.src | grep policypod1

* Delete one namespace and re-add it

kubectl delete -f ~/policy/namespaceproject1.yaml

   # The ACL should have the address_set deleted

kubectl create -f ~/policy/namespaceproject1.yaml

   # The ACL should have the address_set re-added.

* Create new pods in those namespaces.

kubectl create -f ~/policy/nspod1.yaml
kubectl create -f ~/policy/nspod2.yaml

   # the addressSet "project1" and "project2" should get updated


And finally
===========

kubectl delete -f ~/policy/policypod1.yaml
kubectl delete -f ~/policy/policypod2.yaml

   # It should delete all the ACls

* Create them back

kubectl create -f ~/policy/policypod1.yaml
kubectl create -f ~/policy/policypod2.yaml


# Delete the policy

kubectl delete -f ~/policy/policy1.yaml

# All ACLs should get deleted.
# All address_sets (2 associated with 2 ingresspolicies) created for that policy should get deleted

# Create it again

kubectl create -f ~/policy/policy1.yaml


Cleanup
=======

kubectl delete -f ~/policy/policy1.yaml


# Deleting the local peer pods
kubectl delete -f ~/policy/testpod1.yaml
kubectl delete -f ~/policy/testpod2.yaml
kubectl delete -f ~/policy/testpod3.yaml
kubectl delete -f ~/policy/testpod4.yaml

# Deleting the peer namespaces (and pods in it)

kubectl delete -f ~/policy/nspod1.yaml
kubectl delete -f ~/policy/nspod2.yaml

kubectl delete -f ~/policy/namespaceproject1.yaml
kubectl delete -f ~/policy/namespaceproject1twin.yaml
kubectl delete -f ~/policy/namespaceproject2.yaml

# Delete namespace
kubectl delete -f ~/policy/ns1.yaml

