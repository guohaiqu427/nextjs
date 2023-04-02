# Next 13+
    nextjs is a framwork base on react library 

1. setup 
    ``` create-next-app@latest --experimental-app <name>```

2. routinig 
    app 
        |--> page.tsx           :index page         :index 
        about 
            |--> page.tsx       :about page         :file based route 
            me
            |--> page.tsx       :about/me page      :nested route
        blog 
            [...slug]
                |--> page.tsx   :blog/12345         :dynamic route
            |--> page.tsx   
        (marketing)             :grouping / ignored 
            layout.tsx 
            home 
                |--> page.tsx   
        (dashboard)             :grouping / ignored 
            layout.tsx 
            home 
                |--> page.tsx   


3. rendering 
    head.tsx        : meta tags 
    layout.tsx      : effects that layer and layer below it. 
    template.tsx    : re-render everytime 

    navigation  : navigate to page / avoid complete re-render / relative to the root 
    ```ts 
    'use client'
    import Link from "next/link"
    import { useRouter } from "next/navigation";
    
    <Link href="/blog">Blog</Link>
    <Link href={`/blog/[slug]`}>Blog</Link>  
    
    const router = useRouter();     // only available in client component 
    <button type="button" onClick={() => router.push("/blog")}>Blog</button>
    ```

    client component VS server component 
        client component : "use client"
        server component : default 
    
    server component VS SSR
        server component:
            components that never leaves the server, no js shipped to client
                                                                    no broswser API used
                                                                    no hooks 
                                                                    no re-rendering 
                                                                    fetch data the component needs 
                                                                    streaming  data to the client  
        SSR: 
            renders HTML on the server, data asscosiated with the comoponent gets serialized (JSON)
            sents html and serialized data to frontend 
            get hydrayted in frontend = feeds serialized data to frontend 

    client component 
        still can be rendered ono the server staticlly, dynamiclly, dont support async data fetching, can use client side APIs
        when to use client component: when use hooks / class component / DOM event 
        3rd party components that does not have "use client", wrap those component in own client component 

    use client component in server component 
    use server component in client component 


4. data-fetching 
    
    1. server side fetching 
    ```ts
    const getData = async () => {
    const res = await fetch("https://api.cms.com/....");
    return res.json();
    };

    export default async function HomePage() {
    // wow! No hooks on server components!
    const data = await getData();

    return <div>{data.title}</div>;
    }
    ```

    2. API routes 
    ```ts
    export default function handler (req, res) {    
        if(req.method === "post") {                 // different methods are done manually
            res.json({data: {me : true} })
        }
    }
    ```
    API rouotes 
        pages 
            api 
                user 
                    |--> me.ts     
                    
    3. get static params

    ```ts
    // blog/[...slug]/page.tsx
    
    const getPost = async (slug) => {
        conost post = await fetch(....)
        return post
    }

    export default async function Page({ params }) {
    const { slug } = params;
    // use this slug to fetch post data
    const post = await getPost(slug);
        return <div>{post.title}</div>;
    }
    ```

    ```ts
    // blog/page.tsx
    export async function getStaticParams() {
    const posts = await getPosts();

    return posts.map((post) => ({
        slug: post.slug,    // render ahead of time 
    }));
    }
    ```

    4. loading page / error page
    - loading.tsx
    - error.tsx
    - page.tsx

    ```ts
    export default function Loading() {
        return <div>...loading</div>;
    }

    export default function Error() {
        return <div>...error</div>;
    }
    ```

    5. mutating data: update/delete/post
    nextjs recommend refresh  : router.refresh()
    when mutation is done, either lead to a different page or the current page. when its the same page, **refresh**. 

