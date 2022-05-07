```bash
#!/bin/bash

if [ -z $1 ]
    then

        echo "USAGE: ./soso [domain] (optional : subdomainss file to skip first stage)"
        exit
    else
        target=$1
    fi

if [ -z $2 ]
then
    #get subdomainss from subfinder
    echo -e "\e[32m[+]\e[0m Running subfinder..."
    subfinder -d $target -silent > subdomains1.txt
    sf=$(wc subdomains1.txt | awk  '{print $1}') ###
    echo -e "\e[33m[-]\e[0m founded subs:$sf" 
    echo -e "\e[32m[+]\e[0m subfinder tool result saved in subdomains1.txt"
    
    #get subdomainss from assetfinder
    echo -e "\e[32m[+]\e[0m Running assestfinder"
    assetfinder $target -subs-only | grep $domains$ > subdomains2.txt
    sf=$(wc subdomains2.txt | awk  '{print $1}') ###
    echo -e "\e[33m[-]\e[0m founded subs:$sf" 
    echo -e "\e[32m[+]\e[0m subfinder tool result saved in subdomains2.txt"
    
    #get subdomainss from knock
    #echo -e "\e[32m[+]\e[0m Running knock"
    #python3 /home/kali/Desktop/web/tools/knock/knockpy.py $target --no-http > subdomains3.txt
    #sf=$(wc subdomains3.txt | awk  '{print $1}') ###
    #echo -e "\e[33m[-]\e[0m founded subs:$sf" 
    #echo -e "\e[32m[+]\e[0m subfinder tool result saved in subdomains3.txt"
    
    #get subdomainss from sublist3r
    #echo -e "\e[32m[+]\e[0m Running sublist3r"
    #sublist3r -d $target > subdomains4.txt
    #sf=$(wc subdomains4.txt | awk  '{print $1}') ###
    #echo -e "\e[33m[-]\e[0m founded subs:$sf" 
    #echo -e "\e[32m[+]\e[0m sublist3r tool result saved in subdomains4.txt"
    
    #brute force subdomainss
    echo -e "\e[32m[+]\e[0m Running MassDNS"
    awk -v host=$target '{print $0"."host}' /usr/share/SecLists/Discovery/DNS/bitquark-subdomains-top100000.txt   > massdnslist.txt
    massdns massdnslist -r /usr/share/SecLists/Miscellaneous/dns-resolvers.txt -o S -t A -q | awk -F". " '{print $1}' | sort -u > subdomains5.txt
    sf=$(wc subdomains4.txt | awk  '{print $1}') ###
    echo -e "\e[33m[-]\e[0m founded subs:$sf" 
    echo -e "\e[32m[+]\e[0m MassDNS tool result saved in subdomains5.txt"

    echo -e "\e[32m[+]\e[0m the last step makeover"

    cat subdomains*.txt | grep -i $target | sort -u | uniq > finaldomains.txt 
    echo -e "\e[32m[+]\e[0m all domains found is " $(cat finaldomains.txt | wc -l) 
    echo -e "\e[32m[+]\e[0m the tool done now say besm allah and start "
    alldomains=finaldomains.txt
    rm -rf subdomains*.txt
else
    alldomains=$2

fi
    echo -e "\e[92m[+]\e[0m Getting alive domains "
    httpx -l $alldomains -silent -timeout 20 -title -tech-detect -status-code -follow-redirects -o alive_titles.txt
    echo -e "\e[92m[+]\e[0m httpx tool result saved in alive_titles.txt "
    echo -e "\e[92m[+]\e[0m now sort the live domains "
    cat alive_titles.txt | cut -d ' ' -f 1 > alive.txt
    echo -e "\e[92m[+]\e[0m alive domains saved in 'alive.txt' file  "

    echo -e "\e[92m[+]\e[0m Using waybackurls tool with alive.txt "
    
```
