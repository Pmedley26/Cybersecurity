# Phishing Analysis Demonstration



# First Email: “Immediate Action Required: Wire Transfer Request” 

There’s a sense of urgency in the email, which is a common sign of a phishing attempt. In the “From” field, there is a misspelling in the ecorp domain name. This is another common tactic used in phishing emails in an attempt to appear legitimate. The correct domain is ecorp, not “cecorp”. 

If you look at the authentication Results, you see that all three failed. SPF, DKIM, Dmarc. This means that there’s a high probability that this email didn’t come from an authorized or legitimate SMTP server. DKIM failing means that the email was not digitally signed upon coming in, failing another crucial email security check, and DMARC relies heavily on SPF and DKIM to work properly. 


<img width="633" height="547" alt="Phishing Email 1" src="https://github.com/user-attachments/assets/4cbbf295-886f-4eb5-bd34-41c970400a6d" />

