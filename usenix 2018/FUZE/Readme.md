## FUZE: Towards Facilitating Exploit Generation for Kernel Use-After-Free Vulnerabilities

This article is about Kernel Exploit Generation, mainly aimed at automatically generate Kernel Exploit via POC (Proof of Concept).

This article doesn't match my direction in fact, and the author is senior to me, so I don't have to read it such carefully.

The core idea is based on returning to status which are on the path of the POC. In tuition, a path which can't reach exploitation doesn't means the POC is unexploitable, the real exploitable path may share the most part of the origin POC's execution path. So we have to return back to some **important** status, and analyze it.

Here is the **famous** graph that Wu has used in his representation:

![image](https://github.com/kongjiadongyuan/image_in_a_mess/raw/master/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202019-03-02%20%E4%B8%8B%E5%8D%889.20.11.png)

And I think it's clear enough, and I'm too lazy to write more. Good Luck.
