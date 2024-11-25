# FuzzyLand's shuffle64
> Here binary. You find flag.

## Challenge description
We are given a binary executable that takes two arguments, namely a seed and the flag, and that seemingly does some computations with the flag and the seed, outputting some text in the end.

## Solution
The first step is to decompile the program in order to know what it does. One can use an online decompiler or install tools such as Ghidra to do it. This gives us the following code (after removing unnecessary artifacts and renameing some variables and functions):

```c
_UNKNOWN main;
_UNKNOWN init;
_int64 (_fastcall *off_3D98)() = &sub_11C0; // weak
_int64 (_fastcall *off_3DA0)() = &sub_1180; // weak
void *off_4008 = &off_4008; // idb
char s1[] = "t9hNp7fsqjU{rLwIotTv19FC}CeIrZxExFfc3CW1uht1"; // idb
char byte_4050; // weak
__int64 qword_4058; // weak


//----- (00000000000011E3) ----------------------------------------------------
unsigned __int64 pseudoRNG()
{
  unsigned __int64 v1; // [rsp+0h] [rbp-8h]

  v1 = ((((unsigned __int64)qword_4058 >> 12) ^ qword_4058) << 25) ^ ((unsigned __int64)qword_4058 >> 12) ^ qword_4058;
  qword_4058 = (v1 >> 27) ^ v1;
  return (v1 >> 27) ^ v1;
}
// 4058: using guessed type __int64 qword_4058;

//----- (000000000000122B) ----------------------------------------------------
_int64 __fastcall fisher_yates(_int64 a1, __int64 a2)
{
  __int64 result; // rax
  unsigned __int8 v3; // [rsp+13h] [rbp-Dh]
  int v4; // [rsp+14h] [rbp-Ch]
  __int64 i; // [rsp+18h] [rbp-8h]

  result = a2 - 1;
  for ( i = a2 - 1; i; --i )
  {
    v4 = pseudoRNG() % (i + 1);
    v3 = *(_BYTE *)(a1 + i);
    *(_BYTE *)(a1 + i) = *(_BYTE *)(v4 + a1);
    result = v3;
    *(_BYTE *)(a1 + v4) = v3;
  }
  return result;
}

//----- (00000000000012BE) ----------------------------------------------------
__int64 __fastcall main(int a1, char **a2, char **a3)
{
  __int64 v4; // rax
  __int64 v5; // [rsp+18h] [rbp-8h]

  if ( a1 == 3 )
  {
    v5 = strtoul(a2[1], 0LL, 16);
    sub_11C9(v5);
    v4 = strlen(a2[2]);
    sub_122B((__int64)a2[2], v4);
    printf("The output is: %s\n", a2[2]);
    if ( !strcmp(s1, a2[2]) )
    {
      puts("Correct.");
    }
    else if ( qword_4058 == 0xC91C7127A94ED92ELL )
    {
      puts("Almost correct.");
    }
    else
    {
      puts("Keep trying.");
    }
    return 0LL;
  }
  else
  {
    puts("Welcome to shuffle64!");
    puts("Usage: shuffle64 <seed> <flag>");
    return 1LL;
  }
}
```

We can see that the program can output three different texts, depending on whether:
- both the flag and the seed are incorrect
- the seed is correct but the flag isn't
- both the flag and the seed are correct

Actually, it doesn't quite check whether the seed is correct. 