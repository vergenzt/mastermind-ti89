# Mastermind

This is the code for a game of [Mastermind][1] I coded on my TI-89 calculator
in 11th grade during class.

[1]: http://en.wikipedia.org/wiki/Mastermind_(board_game)
The program:

    {"total games","#win","#loss","avglen","lowest"}->mmsc\fmt

    mast()
    Prgm

    Local tfold
    setFold(mastmnd)->tfold
    0->extn

    ClrIO
    Disp " Mastermind!"

    Local name:""->name
    Request "Enter your name",name

    If name="" Then
            Text "Your scores will not be saved."
    Else
            {0,0,0,0,11}->#name
    EndIf

    Local pa:1->pa
    While pa=1

    ClrIO

    Disp " Mastermind!"

    ""->n:""->m

    Local t,t2
    For t,1,4
            n&string(rand(6))->n
    EndFor

    Output 24,125,when(c=0,"????",n)

    1->t
    While t<=10 and m!=n
            getrc(t)->t2
            getguess(t2[1],t2[2])
            If extn=1
                    Goto _exit
            Output t2[1],t2[2],m&" "&mastchk(n,m)
            t+1->t
    EndWhile

    Output 24,125,n
    If m=n Then
            Output 0,80,"You win! :)"
            t-1->t
            If name!="" Then
                    #name[2]+1->#name[2]
            EndIf
    Else
            Output 0,80,"You lose! :("
            If name!="" Then
                    #name[3]+1->#name[3]
            EndIf
    EndIf

    If name!="" Then
            when(t<#name[5],t,#name[5])->#name[5]
            (#name[4]*#name[1]+t)/(#name[1]+1.)->#name[4]
            #name[1]+1->#name[1]
    EndIf

    Pause

    1->pa
    PopUp {"Play again.","Quit."},pa

    EndWhile

    Lbl _exit

    DelVar m,n,_exitn
    setFold(#tfold)
    DispHome
    EndPrgm


    getguess(r,c)
    Prgm

    Local t,s

    Local i:0->i
    ""->s

    Output r,c,"____"

    Local k
    While i<5
            0->k
            // what key did they press?
            While k!=257 and (k<49 or k>54) and k!=13 and k!=264
                    getKey()->k
            EndWhile
            If k=264 Then
            // escape
                    1->extn
                    Return
            ElseIf k=257 and i>0 Then
            // backspace
                    i-1->i
                    mid(s,1,i)->s
                    For t,i,3
                            s&" "->s
                    EndFor
            ElseIf i<4 and k>=49 and k<=54 Then
            // numbers
                    mid(s,1,i)&string(k-48)->s
                    i+1->i
                    For t,i,3
                            s&" "->s
                    EndFor
            ElseIf k=13 and i=4 Then
            // enter
                    5->i
            EndIf
            Output r,c,s
    EndWhile
    s->m	
    EndPrgm

    // checks how well m matches n, returns the mastermind response
    mastchk(n,m)
    Func
            Local c,cp
            0->c:0->cp
            Local t,t2
            
            // convert n and m to lists
            Local nt,mt
            seq("",t,1,4)->nt
            seq("",t,1,4)->mt
            For t,1,4
                    mid(n,t,1)->nt[t]
                    mid(m,t,1)->mt[t]
            EndFor
            
            // remove all exact matches
            For t,1,dim(mt)
                    If nt[t]=mt[t] Then
                            mstmnd\rmel(nt,t)->nt
                            mstmnd\rmel(mt,t)->mt
                            c+1->c
                            t-1->t
                    EndIf
            EndFor
            
            // loop through and remove any spot-unspecific matches
            For t,1,dim(mt)
                    If inString(string(nt),mt[t])>0 Then
                            For t2,1,dim(nt)
                                    If mt[t]=nt[t2] Then
                                            mstmnd\rmel(mt,t)->mt
                                            mstmnd\rmel(nt,t2)->nt
                                            dim(nt)->t2
                                            t-1->t
                                    EndIf
                            EndFor
                    EndIf
            EndWhile
            
            // make the return string, return it
            Local r:""->r
            For t,1,c
                    r&"O"->r
            EndFor
            For t,1,cp
                    r&"o"->r
            EndFor
            
            Return r
            
    EndFunc

    // removes the ith element from list l, returns modified list
    rmel(l,i)
    Func
            Local r,h1,h2,t
            seq(0,x,1,dim(l)-1)->r
            mid(l,1,i-1)->h1
            mid(1,i+1,dim(l)-i)->h2
            For t,1,dim(h1)
                    h1[t]->r[t]
            EndFor
            For t,1,dim(h2)
                    h2[t]->r[t+dim(h1)]
            EndFor
            Return r
    EndFunc

    // returns the row and column number for the given slot (1-10)
    getrc(slot)
    Func
            slot-1->slot
            Return {12*(mod(slot,5)+1), 58*(int(slot/5)+2}
    EndFunc

