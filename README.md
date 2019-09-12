This little demo goes along with my Chef InSpec talks. Here are the steps:


1. boot a Linux machine in your cloud of choice. I prefer Centos 7.* on AWS for this talk.
2. install Chef Workstation:
```
	curl –o cw.rpm https://packages.chef.io/files/stable/chef-workstation/0.8.7/el/7/chef-workstation-0.8.7-1.el7.x86_64.rpm
	sudo yum install -y cw.rpm
```
3. Install git:
```
	sudo yum install -y git
```
4. Clone the linux-baseline InSpec profile:
```
	git clone https://github.com/dev-sec/linux-baseline.git
```
5. Run InSpec (accept the license here too):
```
	sudo inspec exec linux-baseline
```
6. Use the included Chef Policyfile, fix-security.rb or create a new one. You'll accept the chef license here:
```
	chef generate policyfile $POLICY.rb
```
7. Make sure the run-list of your Policyfile is:
```
	run_list: 'os-hardening::default'
```
8. Install the Policy or use the included fix-security.lock.json
```
	chef install $POLICY.rb
	chef export $POLICY.rb run/
	OR
	chef export fix-security.rb run/
```
9. Run chef-client to correct the InSpec reported errors:
```	
	cd run
	sudo chef-client -z
```
10. There will be some things that weren't fixed. This demo includes a new InSpec profile to skip those controls. Create a new profile to wrap linux-baseline or use the included profile:
```
	inspec init profile $PROFILE_NAME
```
11. The inspec.yml file of the profile depends on linux-baseline:
```
	depends:
	  - name: linux-baseline
	    git: https://github.com/dev-sec/linux-baseline
```
12. Create a new control file to skip any failed controls. CentOS usually fails package-08, auditd:
```
	include_controls 'linux-baseline' do
	  skip_control 'package-08'
	end
```
13. Run the new profile:
```
	sudo inspec exec my-hardening
	OR
	sudo inspec exec $PROFILE_NAME
```

NOTE this only runs once on the host. You've fixed everything! If you use a different version of linux, you'll likely get a different set of controls to skip to make the InSpec run pass. That's to be expected as the OSes change.
