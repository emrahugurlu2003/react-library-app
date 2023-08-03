# Genel Olarak Context Yapılar

Theme seçenekleri için Theme Context, kullanıcı login olup olmamasını tutmak için Authenticate Context ve API'den verileri tutmak için Books Context yapıları kullanılacak.

# PriveteRouter İle Sarmalama

About ve Detail'e gitmek isterse, Login olmadıysa önce Login sayfasına yönlendirilecek.

# Login

Herhangi bir veritabanına bağlanmayacak, sadece birşeyler yazmış olması yeterli. Form olduğu için submit butonu tıklanırsa bu kontrolleri yapıyor. Veri girilmemişse submit yapmıyor.

# Routing

Hem id parametresinden yönlendirme hem de sayfalar arası yönlendirme kullanılacak

# Sayfa Componentleri

En dışta bir container ile sarmalanarak, gerekli styling uygulanacak. Komponent bazlı styling için styled-components kütüphanesi import edilecek. Bir komponent için özel bir style uygulanacaksa, Best Practice, onun için ayrı bir Komponent.style.jsx dosyası oluşturmaktır. Bu dosya içinde bir veya birden fazla style tanımlanır. Sarmalamalarda bu tanımlarda belirtilen style uygulanır. Örneğin, Home.style.jsx içinde 3 tane style tanımlıdır: bir tane standart Flex komponenti (CardContainer), bir tane onun `flex-direction: column` olan versiyonu (HomeContainer), bir tane de Flex olmayan ve içinde nested bir img style bulunduran bir div (HomeImage).

# Styled-Components

styled-components, ya bir html element, ya da bir başka komponenti style olarak alıp kullanır. Eğer html elementse styled.element (örneğin: `export const HomeImage = styled.div`) şeklinde; eğer bir komponent ise styled(Komponent) (örneğin: `export const HomeContainer = styled(Flex)`) şeklinde yazılır. Komponent kullanımı, o komponenti extend etmektir. Yani o komponent de içinde çeşitli html elementler içmekte olup, bazı özelliklerini ezmek için extend edilerek yeni style uygulanır. Komponent kullanımı, React'in temel mantığı olup, sık kullanılan stylingler için komponentler oluşturulduktan sonra içlerinde hangi html element için hangi tür styling yapılacağı tanımlanır. Bu projede 2 tane sık kullanılan style mevcut: Flex ve Global.styles .

## Flex

Flex.jsx komponenti, bir div için özel olarak tanımlanmış bazı ayarlamalrı içermektedir. Komponet, prop ile kullanılırsa hangi styling uygulanacak yazılıdır.Örneğin:
###display: flex;
Flex komponentten extend edilen her komponent koşulsuz olarak flex özellikte olsun demektir.
###justify-content: ${({ justify }) => justify || "center"};
Flex komponent eğer justify isimli bir prop ile kullanılırsa justify uygula, prop yoksa center uygula demektir.
###align-items: ${({ align }) => align || "center"};
Flex komponent eğer align isimli bir prop ile kullanılırsa align uygula, prop yoksa center uygula demektir.
###flex-wrap: ${({ wrap }) => wrap || "nowrap"};
Flex komponent eğer wrap isimli bir prop ile kullanılırsa wrap uygula, prop yoksa nowrap uygula demektir.

## Global.styles

Projedeki her komponente uygulanacak style, \* yani root style içinde tanımlıdır. Projedeki her html element için de nested olarak ayrı ayrı tanımlama yapılabilir. Örneğin, body element için satırlar 1.5rem, backgroud-color ise theme context içindeki değerdir. Diğer style komponentleri ya bir html element ya da bir başka komponent kullanırken, Global.styles.jsx komponenti, `createGlobalStyle` isimli bir metodla oluşturulur. Bunun için de `import { createGlobalStyle } from "styled-components";` şeklinde kütüphanenden named import şeklinde import edilerek çağırılır.
#Statik Resimler
Statik resimler, assets/dizini alındaki konumlarından import edilerek kullanılır. Örneğin: `import homeImg from "../../assets/books.jpg";`. Sonra da import sırasında verilmiş olan değişken adı ile komponent olarak kullanılırlar. Örneğin: `<img src={homeImg} alt="Home" />`. Eğer bu resme özel bir style da uygulanacaksa bu komponenti o style için oluşturulmuş komponentle sarmalarız. Örneğin:
`<HomeImage><img src={homeImg} alt="Home" /></HomeImage>`

# Context Tanımlamaları

Best Practice, context tanımlamalarını içeren komponentleri, `context/` isimli dizinin altında tutmaktır. Global State yani Context mantığı genele olarak şöyledir:

1-AaaContext.js içinde `import { createContext, useContext, useState } from "react";` ile import edilir.

2-`const AaaContext = createContext();` ile Context değişkeni oluşturulur.

3-Context içinde tutulacak state `const [myAaa, setMyAaa] = useState("initialAaaValue");` ile oluşturulur.

4-`<AaaContext.Provider value={{ myAaa }}>` ile onu kullanacaklara sunulur. Diğer komponentlerin bir context'i kullanması için, o contexti içeren komponent ile sarmalanmaları gerekir.Context içinde tutulan global veri, başka komponentler tarafından değiştirilecekse, bu veriyi ve onu değiştirien setter metonu da o komponentlere sunmalıyız.
`<AaaContext.Provider value={{ myAaa,setMyAaa }}>`

5-`useContext(AaaContext)` ile de context içinde tutulmuş olan global veri çekilir. Bu şekilde kullanım, useContext hook ile klasik kullanımdır. Dikkat edilirse useContext hook'una bir parametre olarak context'in adı girilmekte ve içinde tuttuğu her neyse return etmesi istenmektedir.

Bunu daha da pratikleştirmek adına return edilen veriyi bir nesne olarak işlemek istersek şöyle de yazabiliriz:
5a-`export const useAaaContext = () => {`
`    return useContext(AaaContext)`
`}`
yani, AaaContext context içinde tutulmuş olan global veriyi return eden `useAaaContext` hook. Artık context'e her ihtiyaç duyduğumuzda tek bir kelimeyle onu çağırabiliriz. `useAaaContext()`. Kullanmak için "context/" dizininden `import { useAaaContext } from "../../context/AaaContext";` şeklinde import edilmelidir.

6-Bu fonksiyon, birincisi global bir state ve ikincisi de onu set eden metod olmak üzere iki global değişken return etmektedir. Bunları destructring ile ayrı ayrı değişkenlere atayabiliriz. Örneğin: `const { myAaa, setMyAaa } = useAaaContext();`

7-Sonuç olarak içinde nested olarak tuttuğu tüm komponentlere contexte erişim sağlayan şey şudur:
`AaaContext.js` içinde birisi default export ve diğeri de named export edilmiş olarak bulunan nesneledir.
|
├── `<AaaContextProvider>`
├──Dafult import yapılması: `import AaaContextProvider from "context/AaaContext";`
├──Kullanımı: `<AaaContextProvider><MyComponent /></AaaContextProvider>`
|
└── `useAaaContext()`
├──Named import yapılması: `import { useAaaContext } from "context/AaaContext";`
└── Kullanımı: `const { myAaa, setMyAaa } = useAaaContext();`

Yani, `AaaContext.js` içinde export edilmiş iki şeyden biri olan `AaaContextProvider`ı import edip`<AaaContextProvider>` ile o komponentleri sarmalamak olup komponentler de aldıkları bu erişim imkanıyla, `AaaContext.js` içinde export edilmiş iki şeyden diğeri olan `useAaaContext`i import ettikten sonra `useAaaContext()` metodu vasıtasıyla, context içindeki veriyi kullanır.

## Context'in Diğer Komponentlere Sunulması

Diğer komponentlerin bir context'i kullanması için, o contexti içeren komponent ile sarmalanmaları gerekir. Eğer bir de o context'i değiştireceklerse, hem context state hem de onu değiştirecek setter metodu birlikte sunulmalıdır. Örneğin:

```
<AaaContext.Provider value={{ myAaa, setMyAaa }}>
 {ContextKullananKomponent1}
 {ContextKullananKomponent2}
</AaaContext.Provider>
```

Bunun için de context'in tanımlandığı komponentin, o context'i bunlarla birlikte bir prop olarak sarmalayan bir AaaContextProvider şekline sokulması gerekir.Her komponent'in sarmaladığı komponentleri tuttuğu `children` adlı bir prop'u bulunur. context'i sunarken bu proptan faydalanılır. Context sunulacak komponent, sarmalanınca onu sarmalayan komponentin `children` larından biri haline gelmiş olur. Sarmalanacak komponenti `children` olarak alıp, üzerine prop olarak Context state ve onun setter metodunu da ekleyerek return ederiz. Böylece bir sarmalayıcı yapı oluştururuz. Bu yapı bir komponenti parametre olarak alıp, onu context state ve setter metodu ile sarmalayıp return eder. Örneğin,

```
import { createContext, useContext, useState } from "react";

const AaaContext = createContext();

const AaaContextProvider = ({ children }) => {
  const [myAaa, setMyAaa] = useState("light");

  return (
    <AaaContext.Provider value={{ myAaa, setMyAaa }}>
      {children}
    </AaaContext.Provider>
  );
};

export const useAaaContext = () => {
    return useContext(AaaContext)
}

export default AaaContextProvider;
```

Context kod blokları "Boiler Plate" yapılar olup, değişkenlik göstermez ve başka bir koddan kopyalanıp, değişken ismi değiştirilip kolayca kullanılabilir.
Burada `useContext(ThemeContext)` yerine bunu return eden sarmalayıcı bir custom Context yapı oluşturulmuş ve `ThemeContextProvider` yazılmıştır. Bunun kullanırken önce import edilir(`import { useThemeContext } from "../../context/ThemeContext";`); sonra da kullanılacak yerde bir fonksiyon olarak çağırılır(`const { myTheme, setMyTheme } = useThemeContext();`)

## ThemeContext.js İçinde Tanımlı ThemeContextProvider

###1-Import Etme Aşaması
`import { createContext, useContext, useState } from "react";`
###2-Context Oluşturma Aşaması
`const ThemeContext = createContext();`
###3-İçine Context Katılarak Sarmalayıcı Component Oluşturulması

```
const ThemeContextProvider = ({ children }) => {
  const [myTheme, setMyTheme] = useState("light");

  return (
    <ThemeContext.Provider value={{ myTheme, setMyTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};
```

###4-Theme için Özel Custom Hook Yazılarak Pratik Hale Getirme ve Named Export işlemi

```
export const useThemeContext = () => {
    return useContext(ThemeContext)
}
```

###5-Default export Etme İşlemi `export default ThemeContextProvider;`
###6-Contexti Kullanacak Komponentin Sarmalanması

```
<ThemeContextProvider>
    <App />
</ThemeContextProvider>
```

###7-Sarmalanmış Komponentte Context Verisinin Kullanılması
`const { myTheme, setMyTheme } = useThemeContext();`
veya
`const { myTheme } = useThemeContext();`
