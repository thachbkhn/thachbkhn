def hex2bin(s):
    mp = {'0': "0000",
          '1': "0001",
          '2': "0010",
          '3': "0011",
          '4': "0100",
          '5': "0101",
          '6': "0110",
          '7': "0111",
          '8': "1000",
          '9': "1001",
          'a': "1010",
          'b': "1011",
          'c': "1100",
          'd': "1101",
          'e': "1110",
          'f': "1111"}
    bin = ""
    for i in range(len(s)):
        bin = bin + mp[s[i]]
    return bin

def bin2hex(s):
    mp = {"0000": '0',
          "0001": '1',
          "0010": '2',
          "0011": '3',
          "0100": '4',
          "0101": '5',
          "0110": '6',
          "0111": '7',
          "1000": '8',
          "1001": '9',
          "1010": 'a',
          "1011": 'b',
          "1100": 'c',
          "1101": 'd',
          "1110": 'e',
          "1111": 'f'}
    hex = ""
    for i in range(0, len(s), 4):
        ch = ""
        ch = ch + s[i]
        ch = ch + s[i + 1]
        ch = ch + s[i + 2]
        ch = ch + s[i + 3]
        hex = hex + mp[ch]

    return hex

def bin2dec(binary):
    binary1 = binary
    decimal, i, n = 0, 0, 0
    while (binary != 0):
        dec = binary % 10
        decimal = decimal + dec * pow(2, i)
        binary = binary // 10
        i += 1
    return decimal

def dec2bin(num):
    res = bin(num).replace("0b", "")
    if (len(res) % 4 != 0):
        div = len(res) / 4
        div = int(div)
        counter = (4 * (div + 1)) - len(res)
        for i in range(0, counter):
            res = '0' + res
    return res

def xor(a,b):
    ans = ""
    for i in range(len(a)):
        if a[i] == b[i]:
            ans = ans + "0"
        else:
            ans = ans + "1"
    return ans

#  ****************************-------KEY_EXPANSION---**************************//////////////

s_box = [["63","7c","77","7b","f2","6b","6f","c5","30","01","67","2b","fe","d7","ab","76"],
         ["ca","82","c9","7d","fa","59","47","f0","ad","d4","a2","af","9c","a4","72","c0"],
         ["b7","fd","93","26","36","3f","f7","cc","34","a5","e5","f1","71","d8","31","15"],
         ["04","c7","23","c3","18","96","05","9a","07","12","80","e2","eb","27","b2","75"],
         ["09","83","2c","1a","1b","6e","5a","a0","52","3b","d6","b3","29","e3","2f","84"],
         ["53","d1","00","ed","20","fc","b1","5b","6a","cb","be","39","4a","4c","58","cf"],
         ["d0","ef","aa","fb","43","4d","33","85","45","f9","02","7f","50","3c","9f","a8"],
         ["51","a3","40","8f","92","9d","38","f5","bc","b6","da","21","10","ff","f3","d2"],
         ["cd","0c","13","ec","5f","97","44","17","c4","a7","7e","3d","64","5d","19","73"],
         ["60","81","4f","dc","22","2a","90","88","46","ee","b8","14","de","5e","0b","db"],
         ["e0","32","3a","0a","49","06","24","5c","c2","d3","ac","62","91","95","e4","79"],
         ["e7","c8","37","6d","8d","d5","4e","a9","6c","56","f4","ea","65","7a","ae","08"],
         ["ba","78","25","2e","1c","a6","b4","c6","e8","dd","74","1f","4b","bd","8b","8a"],
         ["70","3e","b5","66","48","03","f6","0e","61","35","57","b9","86","c1","1d","9e"],
         ["e1","f8","98","11","69","d9","8e","94","9b","1e","87","e9","ce","55","28","df"],
         ["8c","a1","89","0d","bf","e6","42","68","41","99","2d","0f","b0","54","bb","16"]]

Rcon = ["01000000","02000000","04000000","08000000","10000000","20000000","40000000","80000000","1b000000","36000000"]

def RotWord(s):
    rotword = s[2:8] + s[0:2]
    return rotword

def SubWord(s):
    subword = ""
    for i in range(len(s)):
        if i%2==0:
            x = bin2dec(int(hex2bin(s[i])))
            y = bin2dec(int(hex2bin(s[i+1])))
            subword = subword + s_box[x][y]
    return subword

def cipher_key(key_in):
    w=[]
    j=0
    w1=key_in[0:8]
    w2=key_in[8:16]
    w3=key_in[16:24]
    w4=key_in[24:32]

    w.append(w1)
    w.append(w2)
    w.append(w3)
    w.append(w4)
    for i in range(4,44):
        if i%4==0:
            temp = w[-1]
            t = xor(hex2bin(SubWord(RotWord(temp))),hex2bin(Rcon[j]))
            j+=1
            w.append(bin2hex(xor(t,hex2bin(w[i-4]))))
        else:
            temp = w[-1]
            w.append(bin2hex(xor(hex2bin(w[-1]),hex2bin(w[i-4]))))
    return w
#print(cipher_key("2b7e151628aed2a6abf7158809cf4f3c"))

#****************************---------CIPHER-------***********************////////////



def SubByte(state):
    state1 = []
    for i in range(4):
        state1.append(SubWord(state[i][0:2])+SubWord(state[i][2:4])+SubWord(state[i][4:6])+SubWord(state[i][6:8]))
    return state1

def shift_row(state):
    state1 = []
    state1.append(state[0][0:2]+state[1][2:4]+state[2][4:6]+state[3][6:8])
    state1.append(state[1][0:2]+state[2][2:4]+state[3][4:6]+state[0][6:8])
    state1.append(state[2][0:2]+state[3][2:4]+state[0][4:6]+state[1][6:8])
    state1.append(state[3][0:2]+state[0][2:4]+state[1][4:6]+state[2][6:8])
    return state1
#********------HAM NHAN IN GALOIS FEILDS-------******
def nhan(s1,s2):
    kq=""
    if s2=="0010":
        kq=s1+"0"
    elif s2=="0011":
        kq=xor("0"+s1,s1+"0")
    return kq
#********-----CHUYEN STR BIT SANG SO MU IN GALOIS FILD (TRA GIA TRI LIST)------*******
def bin2GF_2_8(c):
    t = []
    reverse_c = c[::-1]
    dem = 0
    for i in range(len(reverse_c)):
        if reverse_c[i] == "1":
           t.append(dem)
           dem +=1
        else:
           dem +=1
    return t
#******HAM IN RA GIA TRI GIONG NHAU IN 2 LIST*******
def difference(list1, list2):
    new_list = []
    for i in list1:
        if i not in list2:
            new_list.append(i)
    for j in list2:
        if j not in list1:
            new_list.append(j)
    return new_list
#******HAM MODULUS TRA GIA TRI LIST Tra Gia Tri Bit*******
gf=[8,4,3,1,0]
def modulus_GF28(a):
    xau = ""
    if max(a)==8:
        for i in range(7,-1,-1):
            if i in difference(a,gf):
                xau += "1"
            else:
                xau += "0"
    else:
        for i in range(7,-1,-1):
            if i in a:
                xau += "1"
            else:
                xau += "0"
    return xau

def mixcolumn(state):
    state1=[]
    for i in range(4):
        a=modulus_GF28(bin2GF_2_8(nhan(hex2bin(state[i][0:2]), "0010")))
        b=modulus_GF28(bin2GF_2_8(nhan(hex2bin(state[i][2:4]), "0011")))
        c=hex2bin(state[i][4:6])
        d=hex2bin(state[i][6:8])
        e=hex2bin(state[i][0:2])
        f=modulus_GF28(bin2GF_2_8(nhan(hex2bin(state[i][2:4]), "0010")))
        g=modulus_GF28(bin2GF_2_8(nhan(hex2bin(state[i][4:6]), "0011")))
        h=hex2bin(state[i][6:8])
        t=hex2bin(state[i][0:2])
        j=hex2bin(state[i][2:4])
        k=modulus_GF28(bin2GF_2_8(nhan(hex2bin(state[i][4:6]), "0010")))
        l=modulus_GF28(bin2GF_2_8(nhan(hex2bin(state[i][6:8]), "0011")))
        m = modulus_GF28(bin2GF_2_8(nhan(hex2bin(state[i][0:2]), "0011")))
        n = hex2bin(state[i][2:4])
        p = hex2bin(state[i][4:6])
        q = modulus_GF28(bin2GF_2_8(nhan(hex2bin(state[i][6:8]), "0010")))

        state1.append(bin2hex(xor(xor(xor(a,b),c),d))+bin2hex(xor(xor(xor(e, f), g), h))+bin2hex(xor(xor(xor(t, j), k), l))+bin2hex(xor(xor(xor(m, n), p), q)))

    #print(bin2hex(xor(xor(xor(a,b),c),d)))
    #print(bin2hex(xor(xor(xor(e, f), g), h)))
    #print(bin2hex(xor(xor(xor(t, j), k), l)))
    #print(bin2hex(xor(xor(xor(m, n), p), q)))


    return state1
state_test = ["a0fafe17","88542cb1","23a33939","2a6c7605"]
def add_round_key(state1,state2):
    state = []
    for i in range(4):
        state.append(bin2hex(xor(hex2bin(state1[i][0:2]),hex2bin(state2[i][0:2])))+bin2hex(xor(hex2bin(state1[i][2:4]),hex2bin(state2[i][2:4])))+bin2hex(xor(hex2bin(state1[i][4:6]),hex2bin(state2[i][4:6])))+bin2hex(xor(hex2bin(state1[i][6:8]),hex2bin(state2[i][6:8]))))
    return state



cp_in="193de3bea0f4e22b9ac68d2ae9f84808"
cp=[]
cp1=cp_in[0:8]
cp2=cp_in[8:16]
cp3=cp_in[16:24]
cp4=cp_in[24:32]

cp.append(cp1)
cp.append(cp2)
cp.append(cp3)
cp.append(cp4)
#print(cp)
#print(SubByte(cp))
#print(shift_row(SubByte(cp)))
#print(mixcolumn(shift_row(SubByte(cp))))

#print(add_round_key(mixcolumn(shift_row(SubByte(cp))),state_test))

def EnCryption(cipher,key):
    cipher_work = []

    cipher_work1 = cipher[0:8]
    cipher_work2 = cipher[8:16]
    cipher_work3 = cipher[16:24]
    cipher_work4 = cipher[24:32]

    cipher_work.append(cipher_work1)
    cipher_work.append(cipher_work2)
    cipher_work.append(cipher_work3)
    cipher_work.append(cipher_work4)

    #print(add_round_key(mixcolumn(shift_row(SubByte(add_round_key(cipher_work,cipher_key(key)[0:4])))),cipher_key(key)[4:8]))

    print("Input 0 : "+str(cipher_work))
    print("Round Key Value 0 : "+str(cipher_key(key)[0:4]))
    print("Input 1 : " + str(add_round_key(cipher_work,cipher_key(key)[0:4])))
    inputtg = add_round_key(cipher_work,cipher_key(key)[0:4])
    inputtg2 = add_round_key(mixcolumn(shift_row(SubByte(inputtg))),cipher_key(key)[4:8])
    inputtg3 = add_round_key(mixcolumn(shift_row(SubByte(inputtg2))),cipher_key(key)[8:12])
    inputtg4 = add_round_key(mixcolumn(shift_row(SubByte(inputtg3))),cipher_key(key)[12:16])
    inputtg5 = add_round_key(mixcolumn(shift_row(SubByte(inputtg4))),cipher_key(key)[16:20])
    inputtg6 = add_round_key(mixcolumn(shift_row(SubByte(inputtg5))),cipher_key(key)[20:24])
    inputtg7 = add_round_key(mixcolumn(shift_row(SubByte(inputtg6))),cipher_key(key)[24:28])
    inputtg8 = add_round_key(mixcolumn(shift_row(SubByte(inputtg7))),cipher_key(key)[28:32])
    inputtg9 = add_round_key(mixcolumn(shift_row(SubByte(inputtg8))),cipher_key(key)[32:36])
    inputtg10 = add_round_key(mixcolumn(shift_row(SubByte(inputtg9))), cipher_key(key)[36:40])

    out_put11 = add_round_key(shift_row(SubByte(inputtg10)),cipher_key(key)[40:44])
    print(out_put11)



dauvao = "00112233445566778899aabbccddeeff"
cipherkey = "000102030405060708090a0b0c0d0e0f"
EnCryption(dauvao,cipherkey)

#****************************--------THE_END----------******************//////

