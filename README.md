<h1><strong><em>FastPaste</em></strong></h1>
<h2>Install</h2>
<hr>
<pre>
<code class="language-python">pip install fastpaste
  or
pip3 install fastpaste==0.0.3</pre></code>


<h2>Documentation</h2>
<hr>
<ul>
<li>
    <h4>
    <details>
        <summary>QuickStart</summary>
        <h5></h4>
        <blockquote>
            <h4>Ngrok Server Paste Example</h4>
            <blockquote>This is an example of a server that forwards any local running port over a ngrok tcp tunnel. Then uploads the randomly generated ngrok tunnel url to <a href="https://www.pastesite.org/about">PasteSite.org<a> using the authour specified during setup. (this is what the client will search to find the tunnel url)</p></blockquote>
            <details><summary>Server.py</summary><br>
            <pre><code class="language-python">import os, fastpaste, pyngrok.ngrok as ngrok, time
authKey=input("What is Your Ngrok Auth Token?: ")
ServerPort=input("What is the port Of The Local Running Server?(Ex. WebServer is 80): ")
Username=input("What is the User you Would Like To Make Pastes With?: ")
fastpaste.Data.UserName=Username
fastpaste.Data.DefSilent=True
if(os.path.exists(os.getcwd()+"/keys.txt")!=True):
    ky=fastpaste.AesObj.gen()
    input(f"Generating Keys.txt For the Encryption. The Client MUST Have The Same Keys.txt For Encryption\n{ky}\nPress Enter To Continue...")
    fastpaste.Data.KeyData=ky
else:
    fastpaste.Data.KeyData=fastpaste.AesObj.keyfile2Tup(os.getcwd()+"/keys.txt")
server=ngrok.connect(f"127.0.0.1:{ServerPort}", "http" if(input("Would You Like To Use Tcp?[y/n]: ")!="y") else "tcp", pyngrok_config=ngrok.conf.PyngrokConfig(auth_token=authKey))
print(f"Started Server At {server.public_url}")
print("Pasteing Encrypted Server Url to pastesite.org")
if(fastpaste.PasteApi.Paste("", str(server.public_url))):
    print("Pasted Successfully")
s=time.time()
while(True):
    try:
        os.system("cls" if(os.sep == "\\") else "clear")
        print(f"Runtime: {fastpaste.Data.time_convert(time.time()-s)}")
        print("Running: True\n")
    except KeyboardInterrupt:
        pass
        try:
            ngrok.disconnect(server.public_url)
        except Exception:
            pass
        os.system("cls" if(os.sep == "\\") else "clear")
        print(f"Final Runtime: {fastpaste.Data.time_convert(time.time()-s)}")
        print("Running: False\n")
        break</code></pre></details>
            <h4>Ngrok Client Paste Example</h4>
            <blockquote>This is an example of a client that will automatically search for pastes from the specified user  on <a href="https://www.pastesite.org/about">PasteSite.org<a> Then Will Decrypt the Ngrok Url Using The SAME keys.txt As the Server.py</p></blockquote>
            <details><summary>Client.py</summary><br>
            <pre><code class="language-python">import os, fastpaste
Username=input("What is the User you Would Like To Make Pastes With?: ")
fastpaste.Data.DefSilent=bool(False if(input("Verbose?[y/n]: ")!='y') else True)
fastpaste.Data.UserName=Username
fastpaste.Data.DefSilent=True
if(os.path.exists(os.getcwd()+"/keys.txt")==True):
    fastpaste.Data.KeyData=fastpaste.AesObj.keyfile2Tup(os.getcwd()+"/keys.txt")
else:
    print("Can't Decrypt Data Without Keys.txt In the Current Dir")
os.system("cls" if(os.sep == "\\") else "clear")
print("Waiting For Paste...\nPress ^C to Stop")
pasteObj, Pastetxt, runtime=fastpaste.PasteApi.WaitForPaste(5)
os.system("cls" if(os.sep == "\\") else "clear")
print(f"Found Paste!\nRuntime: {runtime}\nThe Server Url Is {Pastetxt}")</code></pre></details>
        </blockquote>        
        </h5>
    </details>    
</li>
<li>
    <h4>
    <details>
        <summary>Paste Api</summary>
        <h5>
        <blockquote>
            <details><summary>PasteApi.Paste(title: str, data: str)</summary><br>              
            <pre><code class="language-python"># Description: Creates A New Paste Under The UserName 
PasteApi.Paste(title="Title For New Paste", data="Unencrypted Data To Be Automatically Encrypted And Pasted To pastesite.org")
# Returns True Or Raises an Exception #
            </code></pre></details>
            <details><summary>PasteApi.GetLastPaste()</summary><br>                            
            <pre><code class="language-python"># Description: Retrives The Most Recent Paste Posted To the Site from the Data.UserName as a PasteObj 
PasteApi.GetLastPaste()
            </code></pre></details>
            <details><summary>PasteApi.Scrape(paste: dict)</summary><br>                       
            <pre><code class="language-python"># Description: Retrives The Dencrypted PasteData From the PasteObj As A String
PasteApi.Scrape(paste=PasteObj)
            </code></pre></details>
            <details><summary>PasteApi.SearchByTitle(title: str, NumOfPages: int)</summary><br>
            <pre><code class="language-python"># Description: Retrives A List Of PastObj With The Specified Title
PasteApi.SearchByTitle(title=str("ExampleTitle"), NumOfPages=int(1))
            </code></pre></details>
            <details><summary>PasteApi.SearchByUser(user: str, NumOfPages: int)</summary><br>  
            <pre><code class="language-python"># Description: Retrives A List Of PastObj From The Specified User
PasteApi.SearchByUser(user=str("RandomUser"), NumOfPages=int(1))
            </code></pre></details>
            <details><summary>PasteApi.SearchByLang(lang: str, NumOfPages: int)</summary><br>  
            <pre><code class="language-python"># Description: Retrives A List Of PastObj With The Lang Format
PasteApi.SearchByLang(lang=str('text'), NumOfPages=int(1))
            </code></pre></details>
            <details><summary>PasteApi.WaitForPaste(RefreshTime: int)</summary><br>
            <pre><code class="language-python"># Description: Retrives The Most Recent PasteObj Of the Last Paste From Data.UserName, If No Paste Is Found The Function Will Wait int(RefreshTime) Seconds Before Searching Again Until It Has Found A Paste
PasteApi.WaitForPaste(RefreshTime=int(5))
            </code></pre></details>
            <details><summary>PasteObj And PasteList</summary><br>
            <pre><code class="language-python"># PasteObj is a dict of a paste found on pastesite.org #
PasteObj=dict({
"url":   "Url Of The Paste",              
"code":  "QuickCode Of The Url",             
"title": "Title Of The Paste",               
"user":  "Authour Of The Paste",             
"lang":  "Format of the Paste's data",       
"time":  "How Long Ago The Paste Was Posted" 
})
PasteObj=dict({"url": "https://pastesite.org/view/e2e770be", "code": "e2e770be", "title": "ExampleTitle", "user": "Example UserName", "lang": "text", "time": "4 Minutes ago."})
PasteList=[PasteObj, PasteObj, PasteObj, PasteObj, PasteObj, PasteObj]
            </code></pre></details>
        </blockquote>        
        </h5>
    </details>
</li>
<li>
    <h4>
    <details>
        <summary>AesObj</summary>
        <h5>
        <blockquote>
            <details><summary>AesObj.gen()</summary><br>
            <pre><code class="language-python"># Description: A Function For Generating A Keys.txt File
AesObj.gen()
# Returns a List Of [AesKey, AesIV] that were written to the new keys.txt</code></pre></details>
            <details><summary>AesObj.keyfile2Tup(path: str)</summary><br>
            <pre><code class="language-python"># Description: A Function Used For Getting The Keys From A File
AesObj.keyfile2Tup(path="keys.txt")
# Returns a List Of [AesKey, AesIV] From the Specified File</code></pre></details>
            <details><summary>AesObj.Enc(data: str)</summary><br>        
            <pre><code class="language-python"># Description: Encrypts Passed Data
AesObj.Enc(data="Data To Encrypt")
# Returns a Bytes String Of the Data Encrypted using the keys from Data.KeyData</code></pre></details>
            <details><summary>AesObj.Denc(data: bytes)</summary><br>
            <pre><code class="language-python"># Description: Dencrypts Passed Data
AesObj.Denc(data=b"Bytes String Of Encrypted Data") # For Bytes String You Could Also pass data=bytes("Bytes String Of Encrypted Data".encode('utf8'))
# Returns The Dencrypted Data Of the Bytes String </code></pre></details>
        </blockquote>      
        </h5>
    </details>    
</li>
<li>
    <h4>
    <details>
        <summary>Api</summary>
        <h5>
        <blockquote>
            <details><summary>api.scrapePastes(NumOfPages: int)</summary><br>
            <pre><code class="language-python"># Description: Gets All Pastes Up To the Specified Page Number
api.scrapePastes(NumOfPages=1)
# Returns A List of PasteObj of All Pastes On Every Page </code></pre></details>
            <details><summary>api.paste(title: str, data: str, user: str, expire: str, lang: str, keys: list)</summary><br>
            <pre><code class="language-python"># Description: Creates a Paste
api.paste(title="Example Title", data="Data To Be Encrypted", user="RandomUser", expire=Data.ExpireOptionsList[Data.ExpireInt], keys="Keys To Dencrypt Paste Data")
# Returns True If The Paste Was successful Or False If It Was Not</code></pre></details>
            <details><summary>api.scrapeUser(NumOfPages: int)</summary><br>
            <pre><code class="language-python"># Description: Gets All Of the Pastes Found From The Default User 
# A Quicker Impementaion of Scraping pastesite.org for The Data.UserName 
api.scrapeUser(NumOfPages=1)
# Returns a List Of PasteObj  Of All Pastes Found From The User</code></pre></details>
            <details><summary>api.searchPastes(PasteList: list, **kwargs)</summary><br>
            <pre><code class="language-python"># Description: Filters Pastes In PasteList With The Specified Paramater 
api.searchPastes(PasteList, **kwargs={user="User To Filter For", title="Title To Filter For", lang="Lang/Format To Filter For"})
# Returns List Of PasteObj With The Specified Paramater</code></pre></details>
            <details><summary>api.scrapePaste(paste: dict, keys: list)</summary><br>
            <pre><code class="language-python"># Description: Scrapes The Data From A Paste Using the Keys Passed 
api.scrapePaste(paste=PasteObj, keys="Keys To Dencrypt Paste Data")
# Returns The Dencrypted Data Of the Paste</code></pre></details>
        </blockquote>        
        </h5>
    </details>    
</li>
<li>
    <h4>
    <details>
        <summary>Data</summary>
        <h5>
        <blockquote>
            <pre><code class="language-python">Data.UserName="Default UserName To Search For And Paste To"
Data.KeyData=[AesKey, AesIV]
Data.ExpireInt=1
Data.ExpireOptionsList=["burn", "5", "60", "1440", "10080", "40320", "483840"]
Data.DefScrapeNum=1
Data.DefSilent=False</code></pre>
        </blockquote>        
        </h5>
    </details>    
</li>
</ul>
