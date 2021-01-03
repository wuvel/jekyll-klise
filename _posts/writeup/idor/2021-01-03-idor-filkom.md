---
title: Insecure Direct Object Reference (IDOR) at Filkom Apps
date: 2021-01-03 09:45:47 +07:00
modified: 2021-01-03 23:35:47 +07:00
tags: [write-up]
description: IDOR at Filkom Apps!
---

# Introduction - IDOR
**IDOR (Insecure Direct Object References)** is a security vulnerability in which a user is able to access and make changes to data of any other user present in the system. This will result in a serious security flaw in the web application. This attack is usually occur when the internal implementation object, without any validation mechanism which allows attackers to manipulate these references to access unauthorized data.

In this write up I will explain how i found IDOR vulnerability at <a href="https://filkom.ub.ac.id/apps" target="_blank" rel="noopener">Filkom Apps Website</a> that can leak the short semester pre-KRS data including what subject they take, the GPA, the student's detail (phone number, email, etc.), and proof of  pre-KRS payment.

This attack also reveals the storage patterns / identifiers for the data in the backend and database systems to the attacker. Knowledge of storage identifiers allows the attacker to launch an **enumeration attack**. An enumeration attack can be described as where the attacker builds a collection of valid identifiers using the discovered pattern and test them against the application.

# Proof of Concept
<figure>
<img src="Picture1.png" alt="filkom apps">
<figcaption>Fig 1. Filkom Apps.</figcaption>
</figure>

This happened when I was opening the Filkom Apps website to fill in data for my short semester pre-KRS. After I fill in all the required data, I need to print out my document that i fill earlier with "Cetak Pendataan" menu 👇.

<figure>
<img src="Picture1dot2.png" alt="print pre-krs">
<figcaption>Fig 2. Print short semester document.</figcaption>
</figure>

Then if I press the highlighted button, i got directed to the print page with my Nomor Induk Mahasiswa (NIM) number in the URL 👇.

<figure>
<img src="Picture2.png" alt="print pre-krs page">
<figcaption>Fig 3. Print pre-KRS page.</figcaption>
</figure>

I tried to enter another NIM number (besides my NIM) in the URL and run it in the browser. Here I will use the "185150200111018" NIM. Turn out I can access other student's short semester pre-KRS data! It displays the student's GPA too!

<figure>
<img src="Picture3.png" alt="pre-krs idor">
<figcaption>Fig 4. Pre-KRS IDOR!</figcaption>
</figure>

Secondly, I encountered another IDOR vulnerability at the "Cetak Kuitansi" menu. 

<figure>
<img src="Picture4.png" alt="cetak kuitansi menu">
<figcaption>Fig 5. "Cetak Kuitansi" menu.</figcaption>
</figure>

If I press the "Cetak Kuitansi" button, i got redirected to the print receipt page .

<figure>
<img src="Picture5.png" alt="print receipt page">
<figcaption>Fig 6. Print receipt page.</figcaption>
</figure>

On that page there is a short semester application program form along with the shor semester study plan card. The information shown below are student's NIM, name, study program, GPA of the previous semster, etc. and we can also see additional information in such a phone number and email.

Immediately, I changed my NIM to someone else's NIM. Here, I will use the previous NIM "185150200111018". Again, I can access someone else's short semeser receipt through this IDOR vulnerability!

<figure>
<img src="Picture6.png" alt="receipt idor">
<figcaption>Fig 7. Print receipt page IDOR.</figcaption>
</figure>

Furthermore, I also found IDOR when at the proof of payment menu. We can see the file we uploaded earlier (proof of payment, practicum value, etc.) by pressing the "Lihat ..." menu. Now I clicked to the "Lihat Bukti Bayar" menu that leads to another IDOR.

<figure>
<img src="Picture7.png" alt="proof of payment menu">
<figcaption>Fig 8. "Lihat Bukti Bayar" button.</figcaption>
</figure>

I get a pop-up page containing the file we uploaded earlier (Proof of Payment file).

<figure>
<img src="Picture8.png" alt="proof of payment pop-up">
<figcaption>Fig 9. Proof of Payment pop-up page.</figcaption>
</figure>

Then I observed the process that occurs between the two things above by using the **Developer Tools** on the Mozilla Firefox Browser by pressing the **F12** key.

<figure>
<img src="Picture9.png" alt="developer tools">
<figcaption>Fig 10. Developer Tools.</figcaption>
</figure>

It turns out the browser will make a request to the URL <a href="https://filkom.ub.ac.id/apps" target="_blank" rel="noopener">https://DEPRECATED/BYR_18515020011101720190201.pdf</a> and displays the contents of the page via pop-up page.

<figure>
<img src="Picture10.png" alt="pop-up page">
<figcaption>Fig 11. Pop-up page.</figcaption>
</figure>

If we follow the URL, we can see that the NIM number is being used again in the URL which allows us to do another IDOR! I changed the NIM number in the url and the payload results are <a href="https://filkom.ub.ac.id/apps" target="_blank" rel="noopener">https://DEPRECATED/BYR_18515020011101820190201.pdf</a>. And it worked! We can see other student's Proof of Payment file!

<figure>
<img src="Picture11.png" alt="pop-up page">
<figcaption>Fig 12. Proof of Payment IDOR.</figcaption>
</figure>

# Conclusion
This is occured because of the internal implementation object without any validation mechanism. Thanks for reading my write-up!