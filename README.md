# mnemonic.js

`mnemonic.js` creates arbitrary strength radom pass-phrases (or hexadecimal uids) which you *can* actually remember.

## Huh? My passwords are fine.

Probably not. Your passwords are either not random, or they are not long enough. Or, maybe you are one of those people who **really** have a strong memory, but most of us do not.

Strong, secure passwords need to have an entropy of at least ~80 bits. Here are some real life considerations:

 * If we take trully random passwords from the entire ASCII printable set, we get an entropy of about 6.5 bits/character. That means, you need about 12 randomly chosen characters among the entire set to be ok. Not easy.

 * When you use non-random passwords your entropy/character reduces dramatically. For english, it's about 2 bits/character. You need a lot of characters to get there! And no, adding 1 at the end or using *1337* speak does not help much, dictionary attacks have been countering these techniques for a while now.


## I am sold. How does this work?

`mnemonic.js` uses a relatively small (1626 to be exact) set of english words that are chosen among the [list](http://en.wiktionary.org/wiki/Wiktionary:Frequency_lists/Contemporary_poetry) of frequently used words in contemporary english poetry and are (hopefully) memorable.

To generate a passphrase, a random sequence of 32-bit unsigned integers is generated. The bigger the length of the sequence the stronger it is, for example 4 integers will give you 128-bit strong password. This sequence is then transformed to a list of words from the dictionary, 3 words per integer. The function that transforms the integer `x~i~` to the indexes `w~i~` of the words is the following (1:1 and reversible) function:

    w~i,1~ = x mod n
    w~i,2~ = (x / n + w~i,1~) mod n
    w~i,3~ = (x / n^2 + w~i,2~) mod n

where `n=1626` is the number of words. Repeating for each integer, gives you the sequence of words:

    words = [ dict[w~i,1~], dict[w~i,2~], dict[w~i,3~] for each i ]

As mentioned, you can also do the inverse, i.e. from the list of words calculate the random sequence that produced it. For each triplet of words `word~i,1~`, `word~i,2~`, `word~i,3~`, the integer that corresponds to the triplet is calculated as such:

    w~i,1~ = dict.indexOf(word~i,1~)
    w~i,2~ = dict.indexOf(word~i,2~) mod n
    w~i,3~ = dict.indexOf(word~i,3~) mod n
    x = w~i,1~ + n((w~i,2~ - w~i,1~) mod n) + n^2 ((w~i,3~ - w~i,2~) mod n)

## How do I use it?

In code, to generate a 96-bit password, i.e. 9 words generated from 3 random 32-bit unsigned integers,

```javascript

    m = new Mnemonic(96);
    m.toWords();
    ["grey", "climb", "demon", "snap", "shove", "fruit", "grasp", "hum", "self"]

```

If you wanna see the random sequence, or the the 96-bit number in hexadecimal notation (useful if you need a uid that you can actually remember),

```javascript

    m.random
    [174975897, 171815469, 1859322123]

    m.toHex();
    "0a6deb990a3db22d6ed3010b"

```

## FAQ

 * *Which browsers are supported?*

    At the moment only those which support the [crypto API](https://developer.mozilla.org/en-US/docs/DOM/window.crypto.getRandomValues), used for the random number generation. As of today (22/11/2012) those are Chrome and Safari, but Firefox is almost there. In the future I might include a good PRNG for the browsers that do not support `crypto`.

 * *Why n=1626?*

    Because `n^3 = 4298942376` which is just over `2^32 = 4294967296` which makes the math work while keeping everything in nice multiples of 32 bits.

 * *How does this compare to [diceware](http://world.std.com/~reinhold/diceware.html)?*

    Diceware is very similal and will of course also create secure memorable phrases. It has the advantage of requiring slightly less words (~12.9 bits/word as opposed to ~10.6 for mnemonic) and is also easier to work with without a computer. However, it has a longer list of words (7776) many of which I find impossible to remember ;)

 * *Can I do this in my language?*

    Of course you can! If you happen to make a list of 1626 memorable words in your own language I promise to include it.

## AMD loading

`mnemonic.js` will register as an anonymous module if you use [requireJS](http://requirejs.org/).

## Credits

The idea behind `mnemonic.js` was blatantly stolen from the excellent [electrum](http://electrum.ecdsa.org/) bitcoin client.

## License

`mnemonic.js` is Copyright (C) Yiorgis Gozadinos, Crypho AS. It is open-source and distributed under the MIT License.