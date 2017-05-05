
1.参考 https://docs.chef.io/quick_start.html


https://downloads.chef.io/chefdk/current/1.3.49

2.download and install
curl -O https://packages.chef.io/files/current/chefdk/1.3.49/el/6/chefdk-1.3.49-1.el6.x86_64.rpm

root@lansy01 optilink]# rpm -ivh chefdk-1.3.49-1.el6.x86_64.rpm 
warning: chefdk-1.3.49-1.el6.x86_64.rpm: Header V4 DSA/SHA1 Signature, key ID 83ef826a: NOKEY
Preparing...                ########################################### [100%]
   1:chefdk                 ########################################### [100%]
   Thank you for installing Chef Development Kit!


   [root@lansy01 optilink]# chef -v
   Chef Development Kit Version: 1.3.49
   chef-client version: 12.19.36
   delivery version: master (41b94ffb5efd33723cf72a89bf4d273c8151c9dc)
   berks version: 5.6.4
   kitchen version: 1.16.0
   inspec version: 1.19.1


   3.
   https://docs.chef.io/quick_start.html 里面漏了一步流程

   需要参考 https://github.com/chef/chef-dk


   chef generate recipe ~/pizza_cookbook/cookbooks/pizza_cookbook/ soup

   然后修改
   vim cookbooks/pizza_cookbook/recipes/default.rb

   最后执行
   chef-client --local-mode --override-runlist pizza_cookbook
