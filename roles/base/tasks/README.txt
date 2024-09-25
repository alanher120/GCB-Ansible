
    #!/bin/bash
    # replace string 
    files=2.sh
    method=1 # replace a -> b or remove
    strs='\s*umask\s*[0-9]{1,4}'
    #repl='umask 022'
    #method=2 # replace a xx or a=xx
    #repl='umask 027'
    #strs=`echo $repl|awk '{print $1}'`
    for file in $files;do
    for str in $strs;do
    if [ -f "$file" ];then
    echo "Search string ~ $file"
    grep -E "$str" $file ; retval=$?
    if [ $retval -eq 0 ];then
    if [ $method -eq 1 ];then
    echo "Run method 1"
    if [ -z "$repl" ];then
    sed -ri $file -e "s|($str)| # REMOVE \1|" ; retval=$?
    else
    sed -ri $file -e "s|($str)| $repl |" ; retval=$?
    fi
    fi
    if [ $method -eq 2 ];then
    echo "Run method 1"
    sed -ri $file -e "s|($str).*|$repl|" ; retval=$?
    fi
    fi
    fi
    echo "Verify string ~ at $file"
    grep -E "$str" $file ; retval=$?
    done
    done
    
===
add file 
- name: TWGCB-01-008-0042 TWGCB-01-008-0042 TWGCB-01-008-0232 push /etc/security/limits.d/gcb.conf
  become: true
  copy:
    src: files/{{ansible_distribution}}/{{ansible_distribution_major_version}}/etc/security/limits.d/gcb.conf
    dest: /etc/security/limits.d
    owner: root
    group: root
    mode: 0644


add line text
- name: Add a line to a file if the file does not exist, without passing regexp
  ansible.builtin.lineinfile:
    path: /etc/hosts
    line: 192.168.1.99 foo.lab.net foo
    create: yes


replace string only
- name: Replace old hostname with new hostname (requires Ansible >= 2.4)
  ansible.builtin.replace:
    path: /etc/hosts
    regexp: '(\s*)old\.host\.name(\s+.*)\s*$'
    replace: '\1new.host.name\2'



- name: Replace a localhost entry with our own
  ansible.builtin.lineinfile:
    path: /etc/hosts
    regexp: '^127\.0\.0\.1'
    line: 127.0.0.1 localhost
    owner: root
    group: root
    mode: '0644'



replace abc b or abc=b
    file=/etc/login.defs
    repl='ENCRYPT_METHOD=SHA512'
    echo $repl|grep '=';retval=$?
    if [ $retval -eq 0 ];then
    str1=`echo $repl|awk -F= '{print $1}'`
    str="^\s*${str1}\s*="
    else
    str1=`echo $repl|awk '{print $1}'`
    str="^\s*${str1}"
    fi
    if [ -f $file ];then
    echo "Search string"
    grep -E "$str" $file ; retval=$?
    if [ $retval -eq 0 ];then
    sed -i $file -e "s|$str.*|$repl|" $file ; retval=$?
    else
    echo "$repl" >> $file
    fi
    else
    echo "$repl" >> $file
    fi
    echo "Verify string"
    grep -E "$str" $file

===
cat /etc/yum.repos.d/epel.repo | grep -A 20 "\[epel\]"|while read x;do echo $x | grep "\[";[ $? -eq 0 ] && count=`expr $count + 1`; [ $count -gt 1 ] && break; ;done
===
remove string all line 
    file=/etc/pam.d/su
    str='^\s*auth\s*required\s*pam_wheel.so\s*use_uid.*'
    if [ -f $file ];then
    echo "Search string"
    grep -E "$str" $file ; retval=$?
    if [ $retval -eq 0 ];then
    sed -i $file -e "s|$str| |" $file ; retval=$?
    fi
    echo "Verify string"
    grep -E "$str" $file
    fi

    # remove gpgcheck
    dir=`ls /etc/yum.repos.d/*.repo`
    str='^\s*gpgcheck\s*=.*'
    for file in $dir;do
    if [ -f $file ];then
    echo "Search string"
    grep -E "$str" $file ; retval=$?
    if [ $retval -eq 0 ];then
    sed -i $file -e "s|$str| |" $file ; retval=$?
    fi
    echo "Verify string"
    grep -E "$str" $file
    fi
    done
    
    # add gpgcheck
    dir=`ls /etc/yum.repos.d/*.repo`
    str='\]'
    for file in $dir;do
    if [ -f $file ];then
    echo "Search string"
    grep -E "$str" $file ; retval=$?
    if [ $retval -eq 0 ];then
    sed -i $file -e "s|$str|\]\ngpgcheck=1|" ; retval=$?
    fi
    echo "Verify string"
    grep -E -A1 "$str" $file
    fi
    done

    # remove gpgcheck
    file=/etc/yum.conf
    str='^\s*gpgcheck\s*=.*'
    if [ -f $file ];then
    echo "Search string"
    grep -E "$str" $file ; retval=$?
    if [ $retval -eq 0 ];then
    sed -i $file -e "s|$str| |" $file ; retval=$?
    fi
    echo "Verify string"
    grep -E "$str" $file
    fi
     
    # add gpgcheck
    file=/etc/yum.conf
    str='\]'
    if [ -f $file ];then
    echo "Search string"
    grep -E "$str" $file ; retval=$?
    if [ $retval -eq 0 ];then
    sed -i $file -e "s|$str|\]\ngpgcheck=1|" ; retval=$?
    fi
    echo "Verify string"
    grep -E -A1 "$str" $file
    fi

    file=/etc/ssh/sshd_config
    strs='.*Ciphers.* .*MACS.* .*KexAlgorithms.*' 
    for str in $strs;do
    if [ -f $file ];then
    echo "Search string"
    grep -E "$str" $file ; retval=$?
    if [ $retval -eq 0 ];then
    sed -i $file -e "s|$str| |" $file ; retval=$?
    fi
    fi
    done

remove string   
    file=/etc/sudoers
    str='NOPASSWD'
    if [ -f $file ];then
    echo "Search string"
    grep -E "$str" $file ; retval=$?
    if [ $retval -eq 0 ];then
    sed -i $file -e "s|$str| |" $file ; retval=$?
    echo "Verify string"
    grep -E "$str" $file
    fi
    fi

    dir=/etc/sudoers.d
    str='NOPASSWD'
    for i in `ls $dir/*`;do
    file=$i
    if [ -f $file ];then
    echo "Search string"
    grep -E "$str" $file ; retval=$?
    if [ $retval -eq 0 ];then
    sed -i $file -e "s|$str| |" $file ; retval=$?
    echo "Verify string"
    grep -E "$str" $file
    fi
    fi
    done
    
add comment 

    file=/etc/sudoers
    str='^\s*auth\s*required\s*pam_wheel.so\s*use_uid.*'
    if [ -f $file ];then
    echo "Search string"
    grep -E "$str" $file ; retval=$?
    if [ $retval -eq 0 ];then
    sed -i $file -e "s|$str|# \1|" $file ; retval=$?
    fi
    echo "Verify string"
    grep -E "$str" $file
    fi

    dir=/etc/sudoers.d
    str='^\s*auth\s*required\s*pam_wheel.so\s*use_uid.*'
    for file in `ls $dir/*`;do
    if [ -f $file ];then
    echo "Search string"
    grep -E "$str" $file ; retval=$?
    if [ $retval -eq 0 ];then
    sed -i $file -e "s|$str|# \1|" $file ; retval=$?
    fi
    echo "Verify string"
    grep -E "$str" $file
    fi
    done
    
    
    file=/etc/ssh/sshd_config
    strs='.*Ciphers.* .*MACS.* .*KexAlgorithms.*' 
    for str in $strs;do
    if [ -f $file ];then
    echo "Search string"
    grep -E "$str" $file ; retval=$?
    if [ $retval -eq 0 ];then
    sed -i $file -e "s|$str|# \1|" $file ; retval=$?
    fi
    fi
    done



    file=/etc/sudoers
    str='NOPASSWD'
    gcbtmp=/etc/sudoers.gcbtmp
    echo -n > $gcbtmp
    if [ -f $file ];then
    echo "Search string"
    grep -E "$str" $file ; retval=$?
    if [ $retval -eq 0 ];then
    cat $file|while read x;do
    grep -E "$str" $file >/dev/null 2>&1; retval=$?
    [ $? -eq 0 ] && echo "#"$x >> $gcbtmp
    [ $? -eq 0 ] || echo $x >> $gcbtmp
    done
    cat $gcbtmp > $file
    echo "Verify string"
    grep -E "$str" $file
    fi
    fi
    
    
    
    dir=/etc/sudoers.d
    str='NOPASSWD'
    for i in `ls $dir/*`;do
    file=$i
    gcbtmp=$i.gcbtmp
    echo -n > $gcbtmp
    if [ -f $file ];then
    echo "Search string"
    grep -E "$str" $file ; retval=$?
    if [ $retval -eq 0 ];then
    cat $file|while read x;do
    grep -E "$str" $file >/dev/null 2>&1; retval=$?
    [ $? -eq 0 ] && echo "#"$x >> $gcbtmp
    [ $? -eq 0 ] || echo $x >> $gcbtmp
    done
    cat $gcbtmp > $file
    echo "Verify string"
    grep -E "$str" $file
    fi
    fi
    done
    
    
normal user change cmd    
    cmd="chage --mindays 3000"
    cat /etc/passwd|awk -F: '{ if ( $3 > 999 && $3 < 65534 ) { print $1 } }'|xargs -l $cmd