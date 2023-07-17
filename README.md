# Fuzzing
Automate Fuzzing for Directories or endpoitns on a list of subdomains

Requirnments:
must have installed fuzz and jq.


go install github.com/ffuf/ffuf/v2@latest
or
git clone https://github.com/ffuf/ffuf ; cd ffuf ; go get ; go build

and 
apt install jq -y


To use this you need to add the below code at the end of bashrc file present in users home directory
vim ~/.bashrc
at the end of bashrc paste below command
# fuzz 
        fuzz(){
        mkdir FUZZ
        target=$2
        wordlist=$1
        data=$(echo "${target}" | sed -E 's/[\.|/|:]+/_/g')
        ffuf -w $wordlist -u ${target} -e 'php,jsp,asp,aspx,zip,rar,tar,bkp,bak,sql,sql.gz,html,js,csv,log,cgi,swp,txt,cfm,shtm,htm,phtm,phtml,pl,py,cfg,json,config,pdf,xls,xslx,cpp,conf,doc,docx,bin,bz2,cgi,db,sqlite,do,dump,ini,java,mdb,lst,manifest,old,ora,orig,properties,save,stackdump,swf,temp,trace,xml,cc,~bk,~1,1,0,-old,-OLD,$$$,vbproj,TMP,sav,ORIG,OLD,BACKUP' -od FUZZ -o FUZZ/"${data}".json -maxtime-job 1200 -recursion -recursion-depth 2 -H "User-Agent: Mozilla/5.0" -p 0.1-0.5 -ac -acc admicdgh -acc hsjhsaua -t 1000
        }

        status(){
        code=$1
        jq '.results[] | {url:.url, status:.status, length:.length} | select(.status != null and .status == '${code}')' *.json | jq -r .url
         }

This part is optional and can be removed or left as per requirnment.
#
        -e 'php,jsp,asp,aspx,zip,rar,tar,bkp,bak,sql,sql.gz,html,js,csv,log,cgi,swp,txt,cfm,shtm,htm,phtm,phtml,pl,py,cfg,json,config,pdf,xls,xslx,cpp,conf,doc,docx,bin,bz2,cgi,db,sqlite,do,dump,ini,java,mdb,lst,manifest,old,ora,orig,properties,save,stackdump,swf,temp,trace,xml,cc,~bk,~1,1,0,-old,-OLD,$$$,vbproj,TMP,sav,ORIG,OLD,BACKUP'



USAGE example
#
        cat target.txt | while read -r line; do fuzz /opt/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt $line/FUZZ;done

Then go to newly created FUZZ directory and 
do:
#
        - status 200
        - status 402
        - status 403
        - status 302
        - status 301
        - status 500
