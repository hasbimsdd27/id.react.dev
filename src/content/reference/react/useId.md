---
title: useId
---

<Intro>

`useId` adalah sebuah *Hook* React untuk menghasilkan ID yang unik yang dapat dioper ke atribut aksesibilitas.

```js
const id = useId()
```

</Intro>

<InlineToc />

---

## Referensi {/*referensi*/}

### `useId()` {/*useid*/}

Panggil `useId` pada level teratas dari komponen Anda untuk menghasilkan ID yang unik:

```js
import { useId } from 'react';

function PasswordField() {
  const passwordHintId = useId();
  // ...
```

[Lihat contoh lebih banyak dibawah.](#usage)

#### Parameter {/*parameter*/}

`useId` tidak memerlukan parameter apapun.

#### Pengembalian {/*pengembalian*/}

`useId` mengengembalikan sebuah ID *string* yang unik yang terkait dengan pemanggilan `useId` dalam komponen khusus ini.

#### Peringatan {/*peringatan*/}

* `useId` adalah sebuah *Hook*, sehingga Anda hanya dapat memanggilnya **di tingkat atas komponen Anda** atau *Hooks* Anda sendiri. Anda tidak dapat memanggilnya dalam perulangan atau pengkondisian. Jika Anda membutuhkannya, ekstrak sebuah komponen baru dan pindahkan *state* ke dalamnya.

* `useId` **tidak boleh digunakan untuk menghasilkan *keys*** dalam sebuah daftar. [*Keys* harus dihasilkan dari data Anda.](/learn/rendering-lists#where-to-get-your-key)

---

## Penggunaan {/*penggunaan*/}

<Pitfall>

**Jangan memanggil `useId` untuk menghasilkan *keys* di dalam sebuah daftar.** [*Keys* harus dihasilkan dari data Anda.](/learn/rendering-lists#where-to-get-your-key)

</Pitfall>

### Membuat ID unik untuk atribut aksesibilitas {/*membuat-id-unik-untuk-atribut-aksesibilitas*/}

Panggil `useId` di tingkat atas komponen Anda untuk membuat ID unik:

```js [[1, 4, "passwordHintId"]]
import { useId } from 'react';

function PasswordField() {
  const passwordHintId = useId();
  // ...
```

Anda kemudian dapat mengoper <CodeStep step={1}>ID yang dihasilkan</CodeStep> ke atribut berbeda:

```js [[1, 2, "passwordHintId"], [1, 3, "passwordHintId"]]
<>
  <input type="password" aria-describedby={passwordHintId} />
  <p id={passwordHintId}>
</>
```

**Mari kita telusuri sebuah contoh untuk melihat kapan ini berguna.**

[Atribut aksesibilitas HTML](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA) seperti [`aria-describedby`](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/Attributes/aria-describedby) membiarkan Anda menentukan bahwa dua tag terkait satu sama lain. Misalnya, Anda dapat menentukan bahwa elemen (seperti input) dijelaskan oleh elemen lain (seperti sebuah paragraf).

Dalam HTML biasa, Anda akan menulisnya seperti ini:

```html {5,8}
<label>
  Password:
  <input
    type="password"
    aria-describedby="password-hint"
  />
</label>
<p id="password-hint">
  The password should contain at least 18 characters
</p>
```

Namun, hardcoding ID seperti ini bukanlah praktik yang baik di React. Komponen dapat dirender lebih dari sekali pada halaman--tetapi ID harus unik! Alih-alih melakukan hardcoding ID, buat ID unik dengan `useId`:

```js {4,11,14}
import { useId } from 'react';

function PasswordField() {
  const passwordHintId = useId();
  return (
    <>
      <label>
        Password:
        <input
          type="password"
          aria-describedby={passwordHintId}
        />
      </label>
      <p id={passwordHintId}>
        The password should contain at least 18 characters
      </p>
    </>
  );
}
```

Sekarang, meskipun jika `PasswordField` muncul beberapa kali di layar, ID yang dihasilkan tidak akan bentrok.

<Sandpack>

```js
import { useId } from 'react';

function PasswordField() {
  const passwordHintId = useId();
  return (
    <>
      <label>
        Password:
        <input
          type="password"
          aria-describedby={passwordHintId}
        />
      </label>
      <p id={passwordHintId}>
        The password should contain at least 18 characters
      </p>
    </>
  );
}

export default function App() {
  return (
    <>
      <h2>Choose password</h2>
      <PasswordField />
      <h2>Confirm password</h2>
      <PasswordField />
    </>
  );
}
```

```css
input { margin: 5px; }
```

</Sandpack>

[Tonton video ini](https://www.youtube.com/watch?v=0dNzNcuEuOo) untuk melihat perbedaan dalam pengalaman pengguna dengan teknologi bantu.

<Pitfall>

Dengan [*server rendering*](/reference/react-dom/server), **`useId` Membutuhkan susunan komponen identik pada server dan klien**.  Jika susunan yang Anda render di server dan klien tidak sama persis, ID yang dihasilkan tidak akan cocok.

</Pitfall>

<DeepDive>

#### Mengapa useId lebih baik daripada penghitung inkremental? {/*mengapa-useId-lebih-baik-daripada-penghitung-inkremental*/}

Anda mungkin bertanya-tanya mengapa `useId` lebih baik daripada menambah variabel global seperti `nextId++`.

The primary benefit of `useId` is that React ensures that it works with [server rendering.](/reference/react-dom/server) During server rendering, your components generate HTML output. Later, on the client, [hydration](/reference/react-dom/client/hydrateRoot) attaches your event handlers to the generated HTML. For hydration to work, the client output must match the server HTML.

This is very difficult to guarantee with an incrementing counter because the order in which the client components are hydrated may not match the order in which the server HTML was emitted. By calling `useId`, you ensure that hydration will work, and the output will match between the server and the client.

Inside React, `useId` is generated from the "parent path" of the calling component. This is why, if the client and the server tree are the same, the "parent path" will match up regardless of rendering order.

</DeepDive>

---

### Generating IDs for several related elements {/*generating-ids-for-several-related-elements*/}

If you need to give IDs to multiple related elements, you can call `useId` to generate a shared prefix for them: 

<Sandpack>

```js
import { useId } from 'react';

export default function Form() {
  const id = useId();
  return (
    <form>
      <label htmlFor={id + '-firstName'}>First Name:</label>
      <input id={id + '-firstName'} type="text" />
      <hr />
      <label htmlFor={id + '-lastName'}>Last Name:</label>
      <input id={id + '-lastName'} type="text" />
    </form>
  );
}
```

```css
input { margin: 5px; }
```

</Sandpack>

This lets you avoid calling `useId` for every single element that needs a unique ID.

---

### Specifying a shared prefix for all generated IDs {/*specifying-a-shared-prefix-for-all-generated-ids*/}

If you render multiple independent React applications on a single page, pass `identifierPrefix` as an option to your [`createRoot`](/reference/react-dom/client/createRoot#parameters) or [`hydrateRoot`](/reference/react-dom/client/hydrateRoot) calls. This ensures that the IDs generated by the two different apps never clash because every identifier generated with `useId` will start with the distinct prefix you've specified.

<Sandpack>

```html index.html
<!DOCTYPE html>
<html>
  <head><title>My app</title></head>
  <body>
    <div id="root1"></div>
    <div id="root2"></div>
  </body>
</html>
```

```js
import { useId } from 'react';

function PasswordField() {
  const passwordHintId = useId();
  console.log('Generated identifier:', passwordHintId)
  return (
    <>
      <label>
        Password:
        <input
          type="password"
          aria-describedby={passwordHintId}
        />
      </label>
      <p id={passwordHintId}>
        The password should contain at least 18 characters
      </p>
    </>
  );
}

export default function App() {
  return (
    <>
      <h2>Choose password</h2>
      <PasswordField />
    </>
  );
}
```

```js index.js active
import { createRoot } from 'react-dom/client';
import App from './App.js';
import './styles.css';

const root1 = createRoot(document.getElementById('root1'), {
  identifierPrefix: 'my-first-app-'
});
root1.render(<App />);

const root2 = createRoot(document.getElementById('root2'), {
  identifierPrefix: 'my-second-app-'
});
root2.render(<App />);
```

```css
#root1 {
  border: 5px solid blue;
  padding: 10px;
  margin: 5px;
}

#root2 {
  border: 5px solid green;
  padding: 10px;
  margin: 5px;
}

input { margin: 5px; }
```

</Sandpack>

