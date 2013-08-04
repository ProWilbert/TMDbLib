FORK NOTES
----------
Due to inactivity on the main repo here a slightly updated version with things I found to be broken.

All credit goes to the original author LordMike

TMDbLib
=======

A near-complete wrapper for v3 of TMDb's API (TheMovieDb - https://www.themoviedb.org/).

Nuget
-----

Updated nuget package of my fork of this project can be found at: https://www.nuget.org/packages/Naliath.TMDbLib/

Changelog
---------

**0.7**

 - First release
 - Available on Nuget
 - Basic API design with a great potential for refactoring (be warned on design changes)
 - Supports most (if not all) read-only operations (login sessions not supported - yet).

Examples
--------

Simple example, getting the basic info for "A good day to die hard".

    TMDbClient client = new TMDbClient("APIKey");
    Movie movie = client.GetMovie(47964);
    
    Console.WriteLine("Movie name: {0}", movie.Title);

Using the extra features of TMDb, we can fetch more info in one go (here we fetch casts as well as trailers):

    TMDbClient client = new TMDbClient("APIKey");
    Movie movie = client.GetMovie(47964, MovieMethods.Casts | MovieMethods.Trailers);
    
    Console.WriteLine("Movie title: {0}", movie.Title);
    foreach (Cast cast in movie.Casts.Cast)
        Console.WriteLine("{0} - {1}", cast.Name, cast.Character);

    foreach (Youtube youtube in movie.Trailers.Youtube)
        Console.WriteLine("Trailer: " + youtube.Name);

It is likewise simple to search for people or movies, for example here we search for "007". This yields basically every James Bond film ever made:

    TMDbClient client = new TMDbClient("APIKey");
    SearchContainer<SearchMovie> results = client.SearchMovie("007");
    
    Console.WriteLine("Got {0} of {1} results", results.Results.Count, results.TotalResults);
    foreach (SearchMovie result in results.Results)
        Console.WriteLine(result.Title);

However, another way to get all James Bond movies, is to use the collection-approach. TMDb makes collections for series of movies, such as Die Hard and James Bond. I know there is one, so I will show how to search for the collection, and then list all movies in it:

    TMDbClient client = new TMDbClient("APIKey");
    
    SearchContainer<SearchResultCollection> collectons = client.SearchCollection("James Bond");
    Console.WriteLine("Got {0} collections", collectons.Results.Count);
    
    Collection jamesBonds = client.GetCollection(collectons.Results.First().Id);
    Console.WriteLine("Collection: {0}", jamesBonds.Name);
    Console.WriteLine("Got {0} James Bond Movies", jamesBonds.Parts.Count);
    
    foreach (Part part in jamesBonds.Parts)
        Console.WriteLine(part.Title);

Apiary
------

TMDb have provided an Apiary interface. Apiary is an API documentation service, which also provides a nifty feature that proxies a service through them. It then allows them to log all calls you make to TMDb, and get them shown at Apiary for debugging purposes. This is especially handy if you've set up a client on a server, where it isn't possible to debug web requests.

I use it to debug the library. It ***shouldn't be necesary for you*** to use Apiary for this library, as the library *should* work.

To use this, create an account with Apiary, and view TMDb's API (http://docs.themoviedb.apiary.io/). Open the inspector tab, and note down your personal proxy URL. It looks like this: *http://private-____-themoviedb.apiary.io*.

Instantiate the client like this:

    TMDbClient client = new TMDbClient("APIKey", false, "private-____-themoviedb.apiary.io");

This instructs the client to use the above domain as its base URL. The 2nd parameter (boolean) is whether or not to use SSL.