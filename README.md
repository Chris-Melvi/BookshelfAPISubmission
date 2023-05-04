# BookshelfAPISubmission
program Bookshelf API Submission

//Menambahkan Buku 

const {nanoid} = require('nanoid');
const books = require ('./books');

const addBookHandler = (request,h) => {
    const { name, year, author, summary, publisher, pageCount, readPage, reading} = request.payload;
    if(name === undefined){
        const response = h.response ({
            status: 'fail',
            message: 'Gagal menambahkan buku, masukkan nama buku',
        });
        response.code(400);
        console.log(books);
        return response;
    };
    if(readPage>pageCount){
        const response = h.response ({
            status: 'fail',
            message: 'Gagal menambahkan buku. readPage tidak boleh lebih besar dari pageCount',
        });
        response.code(400);
        return response;
    }
    const id = nanoid(16);
    const insertedAt = new Date().toISOString();
    const updatedAt = insertedAt;
    const finished = (pageCount === readPage);

    const book1 = books.filter((n) => n.finished === true);
    if(finished ){
        return {
            status: 'success',
            message: 'Buku sukses ditambahkan',
        };
    };
    if(readPage<pageCount){
        return {
            status: 'success',
            message: 'Buku sukses ditambahkan',
        };
    };
    const newBook = { 
        id, name, year, author, summary, publisher, pageCount, readPage, finished, reading, insertedAt, updatedAt 
    };
    books.push(newBook);
    const isSuccess = books.filter((book) => book.id === id).length > 0;
    if(isSuccess){
        const response = h.response ({
           status: 'success',
           message: 'Buku berhasil ditambahkan',
           data: {
            bookId: id,
           }, 
        });
        response.code(201);
        console.log(books);
        return response;
    }
    const response = h.response ({
        status: 'fail',
        message: 'Buku tidak berhasil ditambahkan',
    });
    response.code(500);
    return response;
};

//Menampilkan semua buku
const getAllBooksHandler = (request,h) => {
    const { name, reading, finished } = request.query;
    let bookFilter = books;
    if(name !== undefined){
        bookFilter = bookFilter.filter((book) => book.name.toLowerCase().includes(name.toLowerCase()));
    }
    if(reading !== undefined){
        bookFilter = bookFilter.filter((book) => book.reading === !!Number(reading));
    }
    if(finished !== undefined){
        bookFilter = bookFilter.filter((book) => book.finished === !!Number(finished));
    }
    const response = h.response ({
        status: 'success',
        data: {
            books: bookFilter.map((book) =>({
                id: book.id,
                name: book.name,
                publisher: book.publisher,
            })),
        },
    });
    response.code(200);
    return response;
};

//Menampilkan buku berdasarkan ID
const getBookByIdHandler = (request, h) =>{
    const { id } = request.params;
    const book = books.filter((b) => b.id === id)[0];
    if (book !== undefined) {
        return {
            status: 'success',
            data: {
                book,
            },
        };
        response.code(200);
        return response;
    };
    if (book === undefined) {
        const response = h.response({
            status: 'fail',
            message: 'Buku tidak ditemukan',
        });
        response.code(404);
        return response;
    };
};

//Edit buku berdasarkan ID
const editBookByIdHandler = (request, h) => {
    const {id} = request.params;
    const { name, year, author, summary, publisher, pageCount, readPage, reading } = request.payload;
    const insertedAt = new Date().toISOString();
    const updatedAt = insertedAt;
    if(!name){
        const response = h.response({
            status: 'fail',
            message:'Gagal memperbarui buku. Mohon isi nama buku',
        });
        response.code(400);
        return response;
    }
    if(readPage>pageCount){
        const response = h.response({
            status: 'fail',
            message: 'Gagal memperbarui buku. readPage tidak boleh lebih besar dari pageCount',
        });
        response.code(400);
        return response;
    }
    const index = books.findIndex((book) => book.id === id);
    if(index !== -1){
        books[index] = {
            ...books[index],
            name, year, author, summary, publisher, pageCount, readPage, reading,
        };
        const response = h.response({
            status: 'success',
            message: 'Buku berhasil diperbarui',
        });
        response.code(200);
        return response;
    }
    const response = h.response({
        status: 'fail',
        message: 'Gagal memperbarui buku. Id tidak ditemukan',
    });
    response.code(404);
    return response;
};

//DELETE buku
const deleteBookByIdHandler = (request, h) =>{
    const {id} = request.params;
    const index = books.findIndex((book) => book.id === id);
    if(index !== -1){
        books.splice(index, 1);
        const response = h.response({
            status: 'success',
            message: 'Buku berhasil dihapus',
        });
        response.code(200);
        console.log (index);
        return response;
    }
    const response = h.response({
        status: 'fail',
        message: 'Buku gagal dihapus, id tidak ditemukan',
    });
    response.code(404);
    return response;
};
    
module.exports = {addBookHandler, getAllBooksHandler, getBookByIdHandler, editBookByIdHandler, deleteBookByIdHandler};
