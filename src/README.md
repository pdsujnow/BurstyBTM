#Code of Bursty Biterm Topic Model


Bursty biterm topic model (BurstyBTM) is a topic model for bursty discovery in short text streams such as microblogs. 

More detail can be referred to the following paper:

> Xiaohui Yan, Jiafeng Guo, Yanyan Lan, Jun Xu, and Xueqi Cheng. A Probabilistic Model for Bursty Topic Discovery in Microblogs. AAAI2015.

## Usage ##

The code includes a runnable example, you can run it by:

       $ script/runExample.sh

It trains BTM over the documents in *sample-data/doc_info.txt* and output the topics. The doc_info.txt contains all the training documents, where each line represents one document with words separated by space as:
> word1 word2 word3 ....

(*Note: the sample data is only used for illustration of the usage of the code. It is not the data set used in the paper.*)

You can change the paths of data files and parameters in *script/runExample.sh* to run over your own data. 

Indeed, the *runExample.sh* processes the input documents in 4 steps.

**1. Index the words in the documents**   
   To simplify the main code, we provide a python script to map each word to a unique ID (starts from 0) in the documents. 

    $ python script/indexDocs.py <doc_pt> <dwid_pt> <voca_pt>
      doc_pt    input docs to be indexed, each line is a doc with the format "word word ..."
      dwid_pt   output docs after indexing, each line is a doc with the format "wordId wordId ..."
      voca_pt   output vocabulary file, each line is a word with the format "wordId     word"

**2. Statistic daily frequencies for each biterm**

    $ python bitermDayFreq.py <dwid_dir<res_dir>
      dwid_pt   input docs, each line is a doc with the format "word word ..."
      res_pt   output the frequencies of the biterms in the format "w1 w2freq"

**3. Compute eta (bursty probability) in each day for biterms**

    $ python eta.py <n_day> <bf_dir> <res_dir>
	  n_day   number of days to be processed (count from 0)
	  bf_pt   input docs, each line is a biterm with its daily frequency. Line format: "w1 w2    day:freq day:freq ..."
	  res_dir   output the eta of the biterms. Line format: "w1 w2    eta"


**4. Topic learning**  
   The next step is to train the model using the documents represented by word ids.    

    $./src/bbtm <K> <W> <alpha> <beta> <n_iter> <save_step> <biterm_pt> <model_dir> <fix_b>
	 type	's' means simplified BurstyBTM, 'n' means normal BurstyBTM
	 K	int, number of topics, like 20
	 W	int, the size of vocabulary
	 alpha	double, Symmetric Dirichlet prior of P(z), like 1
	 beta	double, Symmetric Dirichlet prior of P(w|z), like 0.01
	 n_iter	int, number of iterations of Gibbs sampling
	 save_step	int, steps to save the results
	 biterm_pt	string, path of training biterms, each line is a biterm with the format 'wi wj eta'
	 model_dir	string, output directory
	 fix_b	'y' means fixing the background word distribution to the empirical word distribution

 
   The results will be written into the directory "model_dir":   
   - k20.pw_z: a K*M matrix for P(w|z), suppose K=20   
   - k20.pz:   a K*1 matrix for P(z), suppose K=20

**3. Inference topic proportions for documents, i.e., P(z|d)**     
   If you need to analysis the topic proportions of each documents, just run the following common to infer that using the model estimated.

    $ ./src/btm inf <type> <K> <docs_pt> <model_dir>
      K	int, number of topics, like 20
      type	 string, 4 choices:sum_w, sum_b, lda, mix. sum_b is used in our  paper.
      docs_pt	string, path of docs to be inferred
      model_dir	string, output directory

   The result will be output to "model_dir":   
   - k20.pz_d: a N*K matrix for P(z|d), suppose K=20
  
**4. Results display**    
   Finally, we also provide a python script to illustrate the top words of the topics and their proportions in the collection. 

    $ python script/topicDisplay.py <model_dir> <K> <voca_pt>
      model_dirthe output dir of BTM
      Kthe number of topics
      voca_ptthe vocabulary file


## History ##
- 2015-01-13, v0.5, clean up
- 2014-09-25, v0.1

If there is any question, feel free to contact: [Xiaohui Yan](http://shortext.org "Xiaohui Yan")(xhcloud@gmail.com).