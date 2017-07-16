---
layout: post
title:  "How to upload file attachments to Salesforce with Python"
---

The are two ways of accessing the Salesforce API via **Simple Object Access**
(**SOAP**) and **REspresentational State Transfer** (**REST**). To programmatically 
upload files or attachments to Salesforce using Python we evaluate first 
how to do it using REST. 

Looking at these docs [Insert or Update Blob Data] 
uploading via REST is only possible if your organization is in the 
Salesforce pilot program. For now we will implement the upload via SOAP 
and using a Python library to access Salesforce named [Beatbox] . 

Below is a command line script I coded to allow one to upload file to 
Salesforce. For local testing I set the **parentId** of the upload file to an
Account named **Ron Local** or set with other value appropriately, 
in production you would normally set the uploaded file to an Email id. 
See also [file upload limitations set by Salesforce]. After successful 
upload you can verify the uploaded file by going to the profile page of the 
Account Ron Local or whatever name of the Account you used.

{% highlight python %}
#!/usr/bin/env python

import sys
import os 
import beatbox

sf = beatbox._tPartnerNS
svc = beatbox.Client()

def main(username, password, file):
    """ For local testing I initially created an Account object in Salesforce
    that has name 'Ron Local'. In production replace parentId with desired id. ie Email id
    """
    loginResult = svc.login(username, password)
    print "sid = " + str(loginResult[sf.sessionId])
    print "welcome " + str(loginResult[sf.userInfo][sf.userFullName])

    account_id = ""
    aq = svc.query("select Id from Account where Name = 'Ron Local'")
    print "query size = " + str(aq[sf.size])
    
    if (str(aq[sf.done]) == 'true'):
        for rec in aq[sf.records:]:
            print str(rec[0]) + " : " + str(rec[1])
            account_id = str(rec[1]) 

    sObject = {
        'type': 'Attachment',
        'Body': base64_encode( read_file(file) ),
        'ParentId': account_id,
        'IsPrivate': False,
        'Name': extract_filename(file),
    } 

    qr = svc.create(sObject)
    if str(qr[sf.success]) == 'true':
        print "upload successfull: id " + str(qr[sf.id])
    else:
        print "upload failed."

def read_file(path):
    output = None
    with open(path) as f:
        output = f.read()
    return output

def base64_encode(content):
    return content.encode(encoding='base64', errors='strict')

def extract_filename(path):
    return os.path.basename(path)


if __name__ == '__main__':
    if len(sys.argv) != 4:
        print "upload2sf.py username password file-path"
    else:
        main(sys.argv[1], sys.argv[2], sys.argv[3])
{% endhighlight %}

**Usage**: Install Beatbox via pip or easy_install:

{% highlight bash %}
pip install beatbox
python upload2sf.py email@somewhere.com password+security-token /Downloads/mastering-vi-vim.pdf
{% endhighlight %}

[file upload limitations set by Salesforce]: https://help.salesforce.com/HTViewHelpDoc?id=collab_files_size_limits.htm 
[Beatbox]: https://github.com/superfell/Beatbox
[Insert or Update Blob Data]: http://www.salesforce.com/us/developer/docs/api_rest/Content/dome_sobject_insert_update_blob.htm 
