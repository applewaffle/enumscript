#Usage : ./enumscript [target]
#Result directory: /root/Desktop/enumresult
#Build by PS 
#20180411

#!/bin/sh
destfolder="/root/Desktop/enumresult"
if [ ! -d "${destfolder}" ]; then
     mkdir "${destfolder}"
fi

target=$1
  if [ ! -d "${destfolder}/${target}" ]; then
     mkdir "${destfolder}/${target}"
  fi
  currentfolder="${destfolder}/${target}"

  #stage 1
  # TCP/80 and 8080
  for p in 80 8080
  do
    echo ${p}
      nmap -v -p${p} -A $target --max-rtt-timeout 3000ms --initial-rtt-timeout 3000ms --max-retries 10 --host-timeout 20m >> ${currentfolder}/nmap-stage1-${p}-result.txt 
      if [ $(grep -ci "open" ${currentfolder}/nmap-stage1-${p}-result.txt) -ne 0 ]; then
         nikto -h http://${target}:${p}/ >> ${currentfolder}/nikto-${p}-result.txt &
         gobuster -t 32 -u http://${target}:${p}/ -w /usr/share/seclists/Discovery/Web_Content/common.txt  -s '200,204,301,302,307,403,500' -e >> ${currentfolder}/gobuster-${p}-result.txt &  
	 xprobe2 -v -p tcp:${p}:open ${target} >> ${currentfolder}/xprobe-${p}-result.txt &

         for f in `ls /usr/share/nmap/scripts/http-vuln*.nse`
         do
            echo ">>>> nmap -v -p${p} --script=${f} ${target}"
            nmap -v -p${p} --script=$f $target --max-rtt-timeout 3000ms --initial-rtt-timeout 3000ms --max-retries 10 --host-timeout 20m --script-timeout 10m >> ${currentfolder}/nmap-stage1-${p}-script-result.txt
            echo
         done
      fi
  done

  # TCP/139/445
      nmap -p139,445 -A $target --max-rtt-timeout 3000ms --initial-rtt-timeout 3000ms --max-retries 10 --host-timeout 20m >> ${currentfolder}/nmap-stage1-445-result.txt 
      if [ $(grep -ci "open" ${currentfolder}/nmap-stage1-445-result.txt) -ne 0 ]; then
	 enum4linux -a ${target} >> ${currentfolder}/enum4linux-stage1-445-result.txt &

         for f in `ls /usr/share/nmap/scripts/smb-vuln*.nse`
         do
            echo ">>>> nmap -v -p139,445 --script=${f} ${target}"
            nmap -v -p139,445 --script=$f $target --max-rtt-timeout 3000ms --initial-rtt-timeout 3000ms --max-retries 10 --host-timeout 20m --script-timeout 10m >> ${currentfolder}/nmap-stage1-445-script-result.txt
            echo
         done
      fi

  # TCP/21

  # UDP/161
      nmap -sU --open -p161 -A $target --max-rtt-timeout 3000ms --initial-rtt-timeout 3000ms --max-retries 10 --host-timeout 20m >> ${currentfolder}/nmap-stage1-161-result.txt 
      if [ $(grep -ci "open" ${currentfolder}/nmap-stage1-161-result.txt) -ne 0 ]; then
         #snmpwalk -c public -v1 $target >> ${currentfolder}/snmpwalk-result.txt &
          snmp-check $target -c public >> ${currentfolder}/snmpcheck-result.txt 
      fi
  currentfolder="${destfolder}/${target}"

  #stage 2 enum Common Ports
  nmap -sS -A -v $target --max-rtt-timeout 3000ms --initial-rtt-timeout 3000ms --max-retries 10 --host-timeout 20m >> ${currentfolder}/nmap-stage2-CommonPorts-result.txt
  currentfolder="${destfolder}/${target}"

  #stage 3 enum for system+registered port
  nmap -sV -T4 $target -p 1-49151 --max-rtt-timeout 3000ms --initial-rtt-timeout 3000ms --max-retries 10 --host-timeout 20m  >> ${currentfolder}/nmap-stage3-RegisteredPorts-result.txt

  currentfolder="${destfolder}/${target}"

  #stage 4 enum Dynamic Ports
  nmap -sV -T4 $target -p 49152-65535 --max-rtt-timeout 3000ms --initial-rtt-timeout 3000ms --max-retries 10 --host-timeout 20m  >> ${currentfolder}/nmap-stage4-DynamicPorts-result.txt 

  currentfolder="${destfolder}/${target}"

  #stage 5 enum UDP Ports
  nmap -sU -sV  -T4 $target --max-rtt-timeout 3000ms --initial-rtt-timeout 3000ms --max-retries 10 --host-timeout 20m  >> ${currentfolder}/nmap-stage5-UDPPorts-result.txt 

