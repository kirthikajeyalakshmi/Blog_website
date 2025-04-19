import React from 'react'
import { Routes , Route } from 'react-router-dom'
import Home from './pages/Home'
import Collection from './pages/Collection'
import About from './pages/About'
import Contact from './pages/Contact'
import Product from './pages/Product'
import Cart from './pages/Cart'
import Login from './pages/Login'
import PlaceOrder from './pages/PlaceOrder'
import Orders from './pages/Orders'
import Navbar from './components/Navbar'
import Footer from './components/Footer'
import Searchbar from './components/Searchbar'
import { ToastContainer, toast } from 'react-toastify';
import 'react-toastify/dist/ReactToastify.css';


const App = () => {
  return (
    <div className='px-4 sm:px-[5vw] md:px-[7vw] lg:px-[9vw]'>
      <ToastContainer/>
      <Navbar/>
      <Searchbar/>
      <Routes>
        <Route path="/" element={<Home/>}/>
        <Route path="/collection" element={<Collection/>}/>
        <Route path="/about" element={<About/>}/>
        <Route path="/contact" element={<Contact/>}/>
        <Route path="/product/:productId" element={<Product/>}/>
        <Route path="/cart" element={<Cart/>}/>
        <Route path="/login" element={<Login/>}/>
        <Route path="/placeorder" element={<PlaceOrder/>}/>
        <Route path="/orders" element={<Orders/>}/>

      </Routes>
      <Footer/>
    </div>
  )
}

export default App

import React, { useContext, useEffect, useState } from 'react'
import { useParams } from 'react-router-dom'
import { ShopContext } from '../context/ShopContext';
import { assets, products } from '../assets/assets';
import RelatedProducts from '../components/RelatedProducts';

const Product = () => {

  const {productId} = useParams();
  const { products, currency, addtocart } = useContext(ShopContext);
  const [productData,setProductData] = useState(false);
  const [image,setImage] = useState('')
  const [size,setSize] =useState('')

  const fetchproductdata = async() =>{
    products.map((item)=>{
      if(item._id === productId){
        setProductData(item)
        setImage(item.image[0])
        console.log(item)
        return null;
      }
    })
  }


  useEffect(()=>{
    fetchproductdata();
  },[productId,products])

  return productData ?  (

    // ui for product page

    <div className='border-t-2 pt-10 transition-opacity ease-in duration-500 opacity-100'>
      {/* product data */}
      <div className='flex gap-12 sm:gap-12 flex-col sm:flex-row'>
        {/* product images */}
        <div className='flex-1 flex flex-col-reverse gap-3 sm:flex-row'>
          <div className='flex sm:flex-col overflow-x-auto sm:overflow-y-scroll justify-between sm:justify-normal sm:w-[18.7%] w-full'>

            {
              productData.image.map((item,index)=>(
                <img onClick={()=>setImage(item)} src={item} key={index} className='w-[24%] sm:w-full sm:mb-3 flex-shrink-0 cursor-pointer' alt="" />
              ))
            }
          </div>

          <div className='w-full sm:w-[80%]'>
            <img className='w-full h-auto' src={image} alt="" />
          </div>
        </div>

        {/* product info */}
        <div className='flex-1'>
          <h1 className='font-medium text-2xl mt-2'>{productData.name}</h1>
          <div className='flex items-center gap-1 mt-2'>
            <img src={assets.star_icon} alt="" className="w-3 5" />
            <img src={assets.star_icon} alt="" className="w-3 5" />
            <img src={assets.star_icon} alt="" className="w-3 5" />
            <img src={assets.star_icon} alt="" className="w-3 5" />
            <img src={assets.star_dull_icon} alt="" className="w-3 5" />
            <p className='pl-2'>(122)</p>
          </div>
          <p className='mt-5 text-3xl font-medium'>{currency}{productData.price}</p>
          <p className='mt-5 text-gray-500 md:w-4/5'>{productData.description}</p>
          <div className='flex flex-col gap-4 my-8'>
            <p>Select Size</p>

            <div className='flex gap-2'>
                {productData.sizes.map((item, index) => (
               <button onClick={()=>setSize(item)} className={`border py-2 px-4 bg-gray-100 ${item === size ? 'border-orange-500' : ''}` } key={index}> {item} </button>
                ))}
              </div>
          </div>
          <button onClick={()=>addtocart(productData._id,size)} className='bg-black text-white px-8 py-3 text-sm active:bg-gray-700'>ADD TO CART</button>
          <hr className='mt-8 sm:w-4/5'/>
          <div className='text-sm text-gray-500 mt-5 flex flex-col gap-1'>
            <p>100% Original product.</p>
            <p>Cash on delivery available on this product. </p>
            <p>Esy return and exchange policy within 7 days.</p>
          </div>
        </div>

      </div>

      {/*  description n review section*/}

      <div className='mt-20'>
        <div className='flex'>
          <p className='border px-5 py-3 text-sm'>Description</p>
          <p className='border px-5 py-3 text-sm'>Reviews (122)</p>
        </div>

        <div className='flex flex-col gap-4 border px-6 py-6 text-sm text-gray-500'>
          <p>Lorem, ipsum dolor sit amet consectetur adipisicing elit. Veniam nam quibusdam atque! Vel nisi excepturi rerum, necessitatibus iste aliquid dolore sapiente esse dicta rem quis a optio odio commodi pariatur deserunt porro quam quia. Enim corporis sed nulla repudiandae velit?</p>
          <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Autem illo molestias debitis suscipit voluptatum magni dolorem quidem excepturi consequuntur laudantium!</p>
        </div>
      </div>

      {/* display related products */}
      
      <RelatedProducts category={productData.category} subCategory={productData.subCategory} />

    </div>
  ) : <div className='opacity-0'> </div>
}

export default Product

import React, { useState } from 'react'
import Title from '../components/Title'
import CardTotal from '../components/CardTotal'
import { assets } from '../assets/assets'
import { useSearchParams } from 'react-router-dom'
import { useContext } from 'react'
import { ShopContext } from '../context/ShopContext'

const PlaceOrder = () => {

  const [method,setMethod] = useState('cod');
  const {navigate} = useContext(ShopContext);

  return (
    <div className='flex flex-col sm:flex-row justify-between gap-4 pt-5 sm:pt-14 min-h-[80vh] border-t'>
      {/* left side */}
      <div className='flex flex-col gap-4 w-full sm:max-w-[480px] '>

        <div className='text-xl sm:text-2xl my-3'>
          <Title text1={'DELIVERY'} text2={'INFORMATION'} />
        </div>

        <div className='flex gap-3'>
          <input className='border border-gray-300 rounded py-1.5 px-3.5 w-full' placeholder='first name' type="text"  />
          <input className='border border-gray-300 rounded py-1.5 px-3.5 w-full' placeholder='last name' type="text"  />
        </div>
        <input className='border border-gray-300 rounded py-1.5 px-3.5 w-full' placeholder='Email address' type="email"  />
        <input className='border border-gray-300 rounded py-1.5 px-3.5 w-full' placeholder='Street' type="text"  />

        <div className='flex gap-3'>
          <input className='border border-gray-300 rounded py-1.5 px-3.5 w-full' placeholder='City' type="text"  />
          <input className='border border-gray-300 rounded py-1.5 px-3.5 w-full' placeholder='State' type="text"  />
        </div>

        <div className='flex gap-3'>
          <input className='border border-gray-300 rounded py-1.5 px-3.5 w-full' placeholder='Zip code' type="number"  />
          <input className='border border-gray-300 rounded py-1.5 px-3.5 w-full' placeholder='Country' type="text"  />
        </div>

        <input className='border border-gray-300 rounded py-1.5 px-3.5 w-full' placeholder='Phone' type="number"  />

      </div>

      {/* right side */}

      <div className='mt-8'>
        <div className='mt-8 min-w-80'>
          <CardTotal/>
        </div>

        <div className='mt-12'>
          <Title text1={'PAYMENT'} text2={'METHOD'} />
          {/* payment method selection */}
          <div className='flex gap-3 flex-col lg:flex-row'>

            <div onClick={()=>setMethod('stripe')} className='flex items-center gap-3 border p-2 px-3 cursor-pointer'>
              <p className={`min-w-3.5 h-3.5 border rounded-full ${method === 'stripe' ? 'bg-green-400' : ''}`}></p>
              <img className='h-5 mx-4' src={assets.stripe_logo} alt="" />
            </div>

            <div onClick={()=>setMethod('razorpay')} className='flex items-center gap-3 border p-2 px-3 cursor-pointer'>
              <p className={`min-w-3.5 h-3.5 border rounded-full ${method === 'razorpay' ? 'bg-green-400' : ''}`}></p>
              <img className='h-5 mx-4' src={assets.razorpay_logo} alt="" />
            </div>

            <div onClick={()=>setMethod('cod')} className='flex items-center gap-3 border p-2 px-3 cursor-pointer'>
              <p className={`min-w-3.5 h-3.5 border rounded-full ${method === 'cod' ? 'bg-green-400' : ''}`}></p>
              <p className='text-gray-500 text-sm font-medium mx-4'>CASH ON DELIVERY</p>
            </div>

          </div>

          <div className='w-full text-end mt-8'>
            <button onClick={()=>navigate('/orders')} className='bg-black text-white px-16 py-3 text-sm'>PLACE ORDER</button>
          </div>
        </div>
      </div>
    </div>
  )
}

export default PlaceOrder

import React from 'react'
import { useContext } from 'react'
import { ShopContext } from '../context/ShopContext'
import Title from '../components/Title';


const Orders = () => {

  const {products,currency} = useContext(ShopContext);


  return (
    <div className='border-t pt-16'>

      <div className='text-2xl'>
        <Title text1={'MY'} text2={' ORDERS'} />
      </div>

      <div>
        {
          products.slice(1,4).map((item,index)=>(
            <div key={index} className='py-4 border-t border-b text-gray-700 flex flex-col md:flex-row md:items-center justify-between gap-4' >
              <div className='flex items-start gap-6 text-sm'>
                <img className='w-16 sm:w-20' src={item.image[0]} alt="" />
                <div>
                  <p className='sm:text-base font-medium'>{item.name}</p>
                  <div className='flex items-center gap-3 mt-2 text-base text-gray-700'>
                    <p className='text-lg'>{currency} {item.price}</p>
                    <p>Quantity: 1</p>
                    <p>Size: M</p>
                  </div>
                  <p className='mt-2'>Date: <span className='text-gray-400'>25, Jul, 2024</span></p>
                </div>
              </div>

              <div className='md:w-1/2 flex justify-between'>
              <div className='flex items-center gap-2'>
                <p className='min-w-2 h-2 rounded-full bg-green-500'></p>
                <p className='text-sm md:text-base'>Ready to ship</p>
              </div>
              <button className='border px-4 py-2 text-sm font-medium rounded-sm '>Track Order</button>
              </div>
            </div>
          ))
        }
      </div>
    </div>
  )
}

export default Orders

import React, { useState } from 'react'

const Login = () => {

  const [currentstate,setCurrentstate] = useState('Login')
  const onSubmitHandler = async (event) =>{

    event.preventDefault();
  }


  return (
    <form onSubmit={onSubmitHandler} className='flex flex-col items-center w-[90%] sm:max-w-96 m-auto mt-14 gap-4 text-gray-800'>
      <div className='inline-flex items-center gap-2 mb-2 mt-10'>
        <p className='prata-regular text-3xl'>{currentstate}</p>
        <hr className='border-none h-[1.5px] w-8 bg-gray-800'/>
      </div>

      {currentstate === 'Login' ? '' : <input type="text" className='w-full px-3 py-2 border border-gray-800' placeholder='Name' required/>}
      <input type="email" className='w-full px-3 py-2 border border-gray-800' placeholder='Email' required/>
      <input type="password" className='w-full px-3 py-2 border border-gray-800' placeholder='Password' required/>

      <div className='w-full flex justify-between text-sm mt-[-8px]'>
        <p className='cursor-pointer'>Forgot your password?</p>
        {
          currentstate === 'Login'
          ? <p onClick={()=>setCurrentstate('Sign Up')} className='cursor-pointer'>Create account</p>
          : <p onClick={()=>setCurrentstate('Login')} className='cursor-pointer'>Login here</p>
        }
      </div>

      <button className='bg-black text-white font-light px-8 py-2 mt-4'>{currentstate === 'Login' ? 'Sign In' : 'Sign Up'}</button>

    </form>
  )
}

export default Login

import React from 'react'
import Hero from '../components/Hero'
import LatestCollection from '../components/LatestCollection'
import BestSeller from '../components/BestSeller'
import Ourpolicy from '../components/Ourpolicy'
import Newsletterbox from '../components/Newsletterbox'

const Home = () => {
  return (
    <div>
      <Hero/>
      <LatestCollection/>
      <BestSeller/>
      <Ourpolicy/>
      <Newsletterbox/>
    </div>
  )
}

export default Home

import React from 'react'
import Title from '../components/Title'
import { assets } from '../assets/assets'
import Newsletterbox from '../components/Newsletterbox'

const Contact = () => {
  return (
    <div>

      <div className='text-center text-2xl pt-10 border-t'>
        <Title text1={'CONTACT'} test2={ 'US'} />
      </div>

      <div className='my-10 flex flex-col justify-center md:flex-row gap-10 mb-28'>
        <img className='w-full md:max-w-[480px]' src={assets.contact_img} alt="" />
        <div className='flex flex-col justify-center items-start gap-6'>

          <p className='font-semibold text-xl text-gray-600'>Our Store</p>
          <p className='text-gray-500'>54709 Willms Station <br/> Suite 350, Washington, USA</p>
          <p className='text-gray-500'>Tel: (415) 555-0132 <br/> Email: admin@forever.com</p>
          <p className='text-gray-500'></p>
          <p className='font-semibold text-xl text-gray-600'>Careers at Forever</p>
          <p className='text-gray-500'>Learn more about our teams and job openings.</p>
          <button className='border border-black px-8 py-4 text-sm hover:bg-black hover:text-white transition-all duration-500'>Explore Jobs </button>

        </div>
      </div>
      
      <Newsletterbox/>
    </div>
  )
}

export default Contact

import React, { useContext, useEffect, useState } from 'react'
import {ShopContext} from '../context/ShopContext'
import { assets } from '../assets/assets'
import Title from '../components/Title'
import Productitem from '../components/Productitem'

const Collection = () => {

  const { products, search, showsearch } = useContext(ShopContext)
  const [showfilter,setShowfilter] = useState(false)
  const [filterproducts,Setfilterproducts] = useState([])
  const [category,setCategory] = useState([]);
  const [subcategory,setSubcategory] = useState([]);
  const [sortType,setSortType] = useState('relevant')

  const togglecategory = (e) =>{

    if(category.includes(e.target.value)){
      setCategory(prev=>prev.filter(item =>item!==e.target.value))
    }
    else{
      setCategory(prev=>[...prev,e.target.value]);
    }
  }

  const togglesubcategory = (e) =>{

    if(subcategory.includes(e.target.value)){
      setSubcategory(prev =>prev.filter(item => item!==e.target.value ))
    }
    else{
      setSubcategory(prev=>[...prev,e.target.value])
    }
  }

  const applyFilter = () =>{
    let productsCopy = products.slice();

    if(showsearch && search){
      productsCopy = productsCopy.filter(item=>item.name.toLowerCase().includes(search.toLowerCase()))
    }

    if(category.length>0){
      productsCopy = productsCopy.filter(item=>category.includes(item.category));
    }
    if (subcategory.length > 0) {
    productsCopy = productsCopy.filter(item => subcategory.includes(item.subCategory)); 
    }
    
    Setfilterproducts(productsCopy)
  }

  //sort

  const sortProduct = () =>{

    let fpcopy = filterproducts.slice();

    switch(sortType){

        case 'low-high':
        Setfilterproducts(fpcopy.sort((a,b)=>(a.price-b.price)))
        break;

        case 'high-low':
          Setfilterproducts(fpcopy.sort((a,b) => (b.price - a.price)))
          break;

          default:
            applyFilter();
            break;
    }

  }

  useEffect(()=>{
    applyFilter();
  },[category,subcategory,search,showsearch])

  useEffect(()=>{
    sortProduct();
  },[sortType])

  return (
    <div className='flex flex-col sm:flex-row gap-1 sm:gap-10 pt-10 border-t'>

      {/* filter options */}

      <div className='min-w-60'>
        <p onClick={()=>setShowfilter(!showfilter)} className='my-2 text-xl flex items-center cursor-pointer gap-2'>FILTERS</p>
        <img className={`h-3 sm:hidden transition-transform duration-200 ${showfilter ? 'rotate-90' : ''}`} 
            src={assets.dropdown_icon} 
            alt="Toggle Filters" />


        {/* category filter */}
        <div className={`border border-gray-300 pl-5 py-3 mt-6 ${showfilter ? '' : 'hidden'} sm:block`}>
          <p className='mb-3 text-sm font-medium'>CATEGORIES</p>
          <div className='flex flex-col gap-2 text-sm font-light text-gray-700'>

            <p className='flex gap-2'>
              <input type="checkbox" className='w-3' value={'Men'} onChange={togglecategory} />Men
            </p>

            <p className='flex gap-2'>
              <input type="checkbox" className='w-3' value={'Women'} onChange={togglecategory} />Women
            </p>

            <p className='flex gap-2'>
              <input type="checkbox" className='w-3' value={'Kids'} onChange={togglecategory} />Kids
            </p>
          </div>
        </div>

          {/* subcategory filter */}

          <div className={`border border_gray-300 pl-5 py-3 mt-6 ${showfilter ? '' : 'hidden'} sm:block`}>
          <p className='mb-3 text-sm font-medium'>TYPE</p>
          <div className='flex flex-col gap-2 text-sm font-light text-gray-700'>

            <p className='flex gap-2'>
              <input type="checkbox" className='w-3' value={'Topwear'} onChange={togglesubcategory} />Topwear
            </p>

            <p className='flex gap-2'>
              <input type="checkbox" className='w-3' value={'Bottomwear'} onChange={togglesubcategory}/>Bottomwear
            </p>

            <p className='flex gap-2'>
              <input type="checkbox" className='w-3' value={'Winterwear'} onChange={togglesubcategory}/>Winterwear
            </p>
          </div>
        </div>

      </div>

      {/* Right side */}
      <div className='flex-1'>

        <div className='flex justify-between text-base sm:text-2xl mb-4'>
          <Title text1={'ALL'} text2={'COLLECTIONS'}/>

          {/* product sort */}
          <select onChange={(e)=>setSortType(e.target.value)} className='border-2 border-gray-300 text-sm px-2'>
          <option value="relevant">Sort by: Relevant</option>
          <option value="low-high">Sort by: low-high</option>
          <option value="high-low">Sort by: high-low</option>
          </select>
        </div>

        {/* Map products */}
        <div className='grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4 gap-y-6'>
        {
             filterproducts.map((item, index) => (
             <Productitem key={index} name={item.name} id={item._id} price={item.price} image={item.image} />
             ))
        }
        </div>
      </div>
      
    </div>
  )
}

export default Collection

import React, { useContext, useEffect, useState } from 'react'
import { ShopContext } from '../context/ShopContext'
import Title from '../components/Title'
import { assets } from '../assets/assets'
import CardTotal from '../components/CardTotal'

const Cart = () => {

  const {products,currency,Cartitems, updatequantity,navigate} = useContext(ShopContext)

  const [cartData,setCartData] = useState([]);

  useEffect(()=>{

    const tempData = [];
    for(const items in Cartitems){
      for(const item in Cartitems[items]){
        if(Cartitems[items][item]>0){
          tempData.push({
            _id:items,
            size: item,
            quantity:Cartitems[items][item]
          })
        }
      }
    }
    setCartData(tempData);

  },[Cartitems])

  return (
    <div className='border-t pt-14'>

      <div className='text-2xl mb-3'>
        <Title text1={'YOUR'} text2={' CART'} />
      </div>

      <div>{
        cartData.map((item,index)=>{

          const productData = products.find((product) => product._id === item._id);

            return (
              <div key={index } className='py-4 border-b text-gray-700 grid grid-cols-[4fr_0.5fr_0.5fr] sm:grid-cols-[4fr_2fr_0.5fr] items-center gap-4' >
                <div className='flex items-start gap-6'>
                  <img className='w-16 sm:w-20' src={productData.image[0]} alt="" />
                  <div>
                    <p className='text-xs sm:text-lg font-medium'>{productData.name}</p>
                    <div className='flex items-center gap-5 mt-2'>
                      <p>{currency} {productData.price}</p>
                      <p className='px-2 sm:px-3 border bg-slate-50'>{item.size}</p>
                    </div>
                  </div>
                </div>
                <input onChange={(e)=>e.target.value === '' || e.target.value === '0' ? null : updatequantity(item._id,item.size,Number(e.target.value))} className='border max-w-10 sm:max-w-20 px-1 sm:px-2 py-1' type="number" min={1} defaultValue={item.quantity}/>
                <img onClick={()=>updatequantity(item._id,item.size,0)} className='w-4 mr-4 sm:w-5 cursor-pointer' src={assets.bin_icon} alt="" />
              </div>
            )
          })
        }

      </div>

      <div className='flex justify-end my-20'>
        <div className='w-full sm:w-[450px]'>
          <CardTotal/>
          <div className='w-full text-end'>
            <button onClick={()=>navigate('/placeorder')} className='bg-black text-white text-sm my-8 px-8 py-3'>PROCEED TO CHECKOUT</button>
          </div>
        </div>

      </div>

    </div>
  )
}

export default Cart

import React from 'react'
import Title from '../components/Title'
import { assets } from '../assets/assets'
import Newsletterbox from '../components/Newsletterbox'

const About = () => {
  return (
    <div>
      <div className='text-2xl text-center pt-8 border-t'>
        <Title text1={'ABOUT'} text2={' US'} />
      </div>

      <div className='my-10 flex flex-col md:flex-row gap-16'>
        <img className='w-full md:max-w-[450px]' src={assets.about_img} alt="" />
        <div className='flex flex-col justify-center gap-6 md:w-2/4 text-gray-600'>
          <p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Aperiam amet assumenda provident repudiandae id! Dolorum sequi minima, ab nisi aut, accusamus vitae itaque reiciendis nobis soluta doloremque! Dolore minima ut provident saepe eum in quis perspiciatis explicabo obcaecati? Ipsa, commodi!</p>
          <p>Lorem ipsum dolor, sit amet consectetur adipisicing elit. Aliquam, similique, voluptas cumque repudiandae, rem ad vero deleniti beatae vitae magnam exercitationem! Enim provident molestiae porro numquam incidunt eligendi ea. Amet eius minus dolorem saepe quibusdam sunt culpa ea deserunt sint.</p>
          <b className='text-gray-800'>Our Mission</b>
          <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Nisi voluptatum facere libero iusto labore a tempore ut illum natus consectetur praesentium corrupti sint, ullam, necessitatibus nam illo excepturi.</p>
        </div>
      </div>

      <div className='text-xl py-4'>
        <Title text1={'WHY'} text2={' CHOOSE US'} />
      </div>

      <div className='flex flex-col md:flex-row text-sm mb-20'>
        <div className='border px-10 md:px-16 py-8 sm:py-20 flex flex-col gap-5'>
          <b>Quality Assurance:</b>
          <p className='text-gray-600' >Lorem ipsum dolor sit, amet consectetur adipisicing elit. Alias, reprehenderit?</p>
        </div>

        <div className='border px-10 md:px-16 py-8 sm:py-20 flex flex-col gap-5'>
          <b>Convenience:</b>
          <p className='text-gray-600'>Lorem ipsum dolor sit, amet consectetur adipisicing elit. Alias, reprehenderit?</p>
        </div>

        <div className='border px-10 md:px-16 py-8 sm:py-20 flex flex-col gap-5'>
          <b>Exceptional Customer Service:</b>
          <p className='text-gray-600'>Lorem ipsum dolor sit, amet consectetur adipisicing elit. Alias, reprehenderit?</p>
        </div>

      </div>

      <Newsletterbox/>
    </div>
  )
}

export default About
import { createContext, useEffect, useState } from "react";
import { products } from "../assets/assets";
import { toast } from "react-toastify";
import {useNavigate} from 'react-router-dom'

export const ShopContext = createContext();

const ShopContextProvider = (props) =>{

    const currency = '₹';
    const delivery_fee = 10;
    const [search,setSearch] = useState('')
    const [showsearch,SetShowSearch] = useState(false)
    const [Cartitems,SetCartitems] = useState({})
    const navigate = useNavigate();

    const addtocart = async (item,size) =>{

        if(!size){
            toast.error('Select product Size')
            return;
        }

        let cartData = structuredClone(Cartitems)

        if(cartData[item]){
            if(cartData[item][size]){
                cartData[item][size]+=1
            }
            else{
                cartData[item][size]=1;
            }
        }
        else{
            cartData[item]={};
            cartData[item][size] = 1;
        }

        SetCartitems(cartData);

    }

    const getcartcount = () =>{
        let totalcount = 0;
        for (const items in Cartitems){
            for(const item in Cartitems[items]){
                try{
                    if(Cartitems[items][item]>0){
                        totalcount += Cartitems[items][item]; 
                    }
                }catch(error){

                }
            }
        }
        return totalcount
    }

    const updatequantity = async (item,size,quantity) =>{
        let cartData = structuredClone(Cartitems)

        cartData[item][size] = quantity

        SetCartitems(cartData);
    }

    const getCartAmount =  () => {
        let totalamount = 0;
        for(const items in Cartitems){
            let iteminfo = products.find((product)=> product._id === items)
            for(const item in Cartitems[items]){
                try{
                    if(Cartitems[items][item]>0){
                        totalamount+=iteminfo.price * Cartitems[items][item]
                    }
                }
                catch(error){

                }
            }
        }
        return totalamount;
    }

    const value = {
        products , currency , delivery_fee,
        search,setSearch,showsearch,SetShowSearch,
        Cartitems,addtocart,
        getcartcount,updatequantity,
        getCartAmount,navigate
    }


    return (
        <ShopContext.Provider value={value}>
            {props.children}
        </ShopContext.Provider>
    )
}

export default ShopContextProvider;

import React, { useContext, useEffect, useState } from 'react'
import { ShopContext } from '../context/ShopContext'
import { assets } from '../assets/assets';
import { useLocation } from 'react-router-dom';

const Searchbar = () => {

    const { search,setSearch,showsearch,SetShowSearch} = useContext(ShopContext);
    const [visisble,setVisible] = useState(false);
    const location = useLocation();

    useEffect(()=>{
      if(location.pathname.includes('collection')){
        setVisible(true);
      }
      else{
        setVisible(false);
      }
    },[location])

  return showsearch && visisble ?  (
    <div className='border-t border-b bg-gray-50 text-center'>
        <div className='inline-flex items-center justify-center border border-gray-400 px-5 py-2 my-5 mx-3 rounded-full w-3/4 sm:w-1/2'>
        <input value={search} onChange={(e)=>setSearch(e.target.value)} className='flex-1 outline-none bg-inherit text-sm ' type="text" placeholder='search'/>
        <img className='w-4' src={assets.search_icon}/>
        </div>
        <img onClick={()=>SetShowSearch(false)} className='inline w-3 cursor-pointer' src={assets.cross_icon} alt="" />
    </div>
  ) : null
}

export default Searchbar

import React, { useContext, useEffect, useState } from 'react'
import { ShopContext } from '../context/ShopContext'
import Title from '../components/Title'
import Productitem from '../components/Productitem'

const RelatedProducts = ({category,subCategory}) => {

    const {products} = useContext(ShopContext)
    const [related,setRelated] = useState([])

    useEffect(()=>{

        if(products.length>0){

            let productsCopy = products.slice();

            productsCopy = productsCopy.filter((item)=> category === item.category)
            productsCopy=productsCopy.filter((item)=> subCategory === item.subCategory)
            
            setRelated(productsCopy.slice(0,5))
        }
    },[products])
  return (
    <div className='my-24'>
        <div className='text-center text-3xl py-2'>
            <Title text1={'RELATED'} text2={'PRODUCTS'}/>
        </div>

        <div className='grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-5 gap-4 gap-y-6'>
            {related.map((item,index)=>(
                <Productitem key={index} id={item._id} name={item.name} size={item.size} price={item.price} image={item.image}/>
            ))}
        </div>
    </div>
  )
}

export default RelatedProducts

import React, { useContext } from 'react'
import { ShopContext } from '../context/ShopContext'
import { Link } from 'react-router-dom'

const Productitem = ({id,image,name,price}) => {

    const {currency} = useContext(ShopContext)

  return (
    <Link className='text-gray-700 cursor-pointer' to={`/product/${id}`}>
        <div className='overflow-hidden'>
            <img className='hover:scale-110 transition ease-in-out' src={image[0]} alt="" />
        </div>
        <p className='pt-3 pb-1 text-sm'> {name}</p>
        <p className='text-sm font-medium'>{currency} {price}</p>
    </Link>
  )
}

export default Productitem

import React from 'react'
import { assets } from '../assets/assets'

const Ourpolicy = () => {
  return (
    <div className='flex flex-col sm:flex-row justify-around gap-12 sm:gap-2 text-center py-20 text-xs sm:text-sm md:text-base text-gray-700'>
        <div>
            <img src={assets.exchange_icon} className='w-12 m-auto mb-5' alt="" />
            <p className='font-semibold'>Easy Exchange Policy</p>
            <p className='text-gray-400'>We offer hassle free exchange policy</p>
        </div>

        <div>
            <img src={assets.quality_icon} className='w-12 m-auto mb-5' alt="" />
            <p className='font-semibold'>7 Days Return Policy</p>
            <p className='text-gray-400'>We Provide 7 Days free return policy</p>
        </div>

        <div>
            <img src={assets.support_img} className='w-12 m-auto mb-5' alt="" />
            <p className='font-semibold'>Best Customer Support</p>
            <p className='text-gray-400'>We provide 24/7 customer support</p>
        </div>
    </div>
  )
}

export default Ourpolicy

import React from 'react'

const Newsletterbox = () => {

    const onSubmitHandler = (event)=>{
        event.preventDefault();
    }
  return (
    <div className='text-center'>
        <p className='text-2xl font-medium text-gray-800'>Subscribe now & get 20% off</p>
        <p className='text-gray-400 mt-3'>Lorem ipsum dolor sit amet consectetur adipisicing elit.</p>

        <form onSubmit={onSubmitHandler} className='w-full sm:w-1/2 flex items-center gap-3 mx-auto my-6 border pl-3'>
            <input className='w-full sm:flex-1 outline-none' type="email" placeholder='Enter your email' required/>
            <button type='submit' className='bg-black text-white text-xs px-10 py-4'>SUBSCRIBE</button>
        </form>

    </div>
  )
}

export default Newsletterbox

import React, { useContext, useState } from 'react'
import {assets} from '../assets/assets'
import { NavLink , Link } from 'react-router-dom'
import { ShopContext } from '../context/ShopContext';


const Navbar = () => {

    const [visisble,setVisisble] = useState(false);

    const {SetShowSearch, getcartcount} = useContext(ShopContext)

  return (

    <div className='flex items-center justify-between py-5 font-medium'>

     <Link to='/'>
           <img src={assets.logo} className='w-36' alt='Logo' />
     </Link>

        <ul className='hidden sm:flex gap-5 text-sm text-gray-700 mx-auto'>

            <NavLink to='/' className='flex flex-col items-center gap-1'>
                <p>HOME</p>
                <hr className='w-[50px] border-none h-[1.5px]  bg-gray-700 hidden'/>
            </NavLink> 

            <NavLink to='/collection' className='flex flex-col items-center gap-1'>
                <p>COLLECTION</p>
                <hr className='w-2/4 border-none h-[1.5px] bg-gray-700 hidden'/>
            </NavLink> 

            <NavLink to='/about' className='flex flex-col items-center gap-1'>
                <p>ABOUT</p>
                <hr className='w-2/4 border-none h-[1.5px] bg-gray-700 hidden'/>
            </NavLink> 

            <NavLink to='/contact' className='flex flex-col items-center gap-1'>
                <p>CONTACT</p>
                <hr className='w-2/4 border-none h-[1.5px] bg-gray-700 hidden'/>
            </NavLink>    
        </ul>

        <div className='flex items-center gap-6'>
            <img onClick={()=>(SetShowSearch(true))} src={assets.search_icon} className='w-5 cursor-pointer'/>

            <div className='relative group'>
                <Link to='/login'><img src={assets.profile_icon} className='w-5 cursor-pointer' /></Link>
                
                <div className='group-hover:block hidden absolute dropdown-menu right-0 pt-4'>
                    <div className='flex flex-col gap-2 w-36 py-3 px-5 bg-slate-100 text-gray-500 rounded'>
                        <p className='cursor-pointer hover:text-black'>My Profile</p>
                        <p className='cursor-pointer hover:text-black'>Orders</p>
                        <p className='cursor-pointer hover:text-black'>Logout</p>
                    </div>
                </div>
            </div>

            <Link to="./cart" className='relative'>
            <img src={assets.cart_icon} className='w-5 min-w-5'/>
            <p className='absolute right-[-5px] bottom-[-5px] w-4 text-center leading-4 bg-black text-white aspect-square rounded-full text-[8px]'>{getcartcount()}</p>
            </Link>

            <img onClick={()=>setVisisble(true)} src={assets.menu_icon} className='w-5 cursor-pointer sm:hidden' alt="" />
            
        </div>

        {/* sidebar menu for smaller screens*/}

        <div className={`absolute top-0 right-0 bottom-0 overflow-hidden bg-white transition-all ${visisble ? 'w-full' : 'w-0'}`}>
            <div className='flex flex-col text-gray-600'>
                <div onClick={()=>setVisisble(false)} className='flex items-center gap-4 p-3 cursor-pointer'>
                    <img className='h-4 rotate-180' src={assets.dropdown_icon}/>
                    <p>Back</p>
                </div>

                <NavLink onClick={()=>setVisisble(false)} className="py-2 pl-6 border" to='/'>HOME</NavLink>
                <NavLink onClick={()=>setVisisble(false)} className="py-2 pl-6 border" to='/collection'>COLLECTION</NavLink>
                <NavLink onClick={()=>setVisisble(false)} className="py-2 pl-6 border" to='/about'>ABOUT</NavLink>
                <NavLink onClick={()=>setVisisble(false)} className="py-2 pl-6 border" to='/contact'>CONTACT</NavLink>

            </div>
        </div>

        
    </div>
  )
}

export default Navbar

import React, { useContext, useEffect, useState } from 'react'
import { ShopContext } from '../context/ShopContext';
import Title from './Title';
import Productitem from './Productitem';


const LatestCollection = () => {

    const { products } = useContext(ShopContext);
    
    const [latestproducts,setLatestproducts] = useState([]);

    useEffect(()=>{
        setLatestproducts(products.slice(0,10));
    },[])

  return (
    <div className='my-10'>
        <div className='text-center py-8 text-3xl'>
            <Title text1={'LATEST'} text2={' COLLECTIONS'} />
            <p className='w-3/4 m-auto text-xs sm:text-sm md:text-base text-gray-600'>
            Lorem ipsum, dolor sit amet consectetur adipisicing elit. Esse dolore, accusantium assumenda error cumque
            </p>
        </div>


    {/* displaying data */}

    <div className='grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-5 gap-6 gap-y-6 p-4'>
        
        {
            latestproducts.map((item, index) => (
                <Productitem key={index} id={item._id} image={item.image} name={item.name} price={item.price} />
            ))
            
        }
    </div>
    </div>
  )
}

export default LatestCollection

import React from 'react'
import { assets } from '../assets/assets'

const Hero = () => {
  return (
    <div className='flex flex-col sm:flex-row border border-gray-400'>
        {/* Hero left side*/}
        <div className='w-full sm:w-1/2 flex items-center justify-center py-10 sm:py-0'>
        <div className='text-[#414141]'>
            <div className='flex items-center gap-2'>
                <p className='w-8 md:w-11 h-[2px] bg-[#414141]'></p>
                <p className='font-medium text-sm md:text-base'>OUR BESTSELLERS</p>
            </div>
            <h1 className='prata-regular text-3xl sm:py-3 lg:text-5xl leading-relaxed'>Latest Arrivals</h1>
            <div className='flex items-center gap-2'>
                <p className='font-semibold text-sm md:text-base'>SHOP NOW</p>
                <p className='w-8 md:w-11 h-[2px] bg-[#414141]'></p>
            </div>
        </div>
        </div>
        {/* Hero right side */}
        <img className='w-full sm:w-1/2' src={assets.hero_img}/>

    </div>
  )
}

export default Hero

import React from 'react'
import { assets } from '../assets/assets'

const Footer = () => {
  return (
    <div>
        <div className='flex flex-col sm:grid grid-cols-[3fr_1fr_1fr] gap-14 my-10 mt-40 text-sm'>
            <div>
                <img src={assets.logo} className='mb-5 w-32' alt="" />
                <p className='w-full md:w-2/3 text-gray-600'>Lorem, ipsum dolor sit amet consectetur adipisicing elit. Qui incidunt esse fugiat amet a corporis pariatur beatae praesentium et est Lorem ipsum dolor sit amet.</p>
            </div>

            <div>
                <p className='text-xl font-medium mb-5'>COMPANY</p>
                <ul className='flex flex-col text-gray-600'>
                    <li>Home</li>
                    <li>About us</li>
                    <li>Delivery</li>
                    <li>Privacy policy</li>
                </ul>
            </div>

            <div>
                <p className='text-xl font-medium mb-5'>GET IN TOUCH</p>
                <ul className='flex flex-col text-gray-600'>
                    <li>+1-212-456-7980</li>
                    <li>contact@foreveryou.com</li>
                </ul>
            </div>
        </div>

        <div>
            <hr />
            <p className='py-5 text-sm text-center'>Copyright 2024@ forever.com - All Right Reserved.</p>
        </div>
    </div>
  )
}

export default Footer
