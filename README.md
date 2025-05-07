   AMITY UNIVERSITY MUMBAI
     AMITY SCHOOL OF ENGINEERING AND TECHNOLOGY
Department of Computer Science & Engineering
Academic Year 2024-25




Final Year Project on
“Game Control Using EEG Based BCI”

Submitted in partial fulfilment of the requirements for the degree of

Bachelor of Technology
Department of Computer Science & Engineering 



Submitted By

Name     	                    Enrolment no
1. Amey Patil
2. Soham Jayakar
3. Kirtan Dhinoja
4. Mohmad Danish Rehman	A70405221182
A70405221142
A70405221200
A70405221104
	


Under the guidance of
Dr. Rajiv Iyer
 

Declaration of Academic Integrity


We declare that this written submission conveys our ideas in our own words. We have adequately cited and referenced the original sources. We also declare that we have adhered to all principles of academic honesty and integrity and have not misrepresented or fabricated or falsified any idea/date/fact/source in our submission.

We understand that any violation of the above will be cause for disciplinary action by the institute and they can evoke penal action from the sources which have thus not been properly cited or from whom proper permission has not been taken when needed.



Student Signature
                                         1. Amey Patil
2. Soham Jayakar
3. Kirtan Dhinoja
                 4. Mohmad Danish Rehman

	


 


Approval


This is to certify that “Amey Patil, Soham Jayakar, Kirtan Dhinoja, Mohmad Danish Rehman” have satisfactorily completed their project (final stage) on “Game Control Using EEG Based BCI” during the academic term 2024-25 and their report is approved for final submission.



Examiners


………………………


……………………….


……………………….


Date: Place:
 



CERTIFICATE


This is to certify that the project entitled “Game Control Using EEG Based BCI” is a bonafide work of “Amey Patil, Soham Jayakar, Kirtan Dhinoja, Mohmad Danish Rehman” submitted to the Amity School of Engineering and Technology, Amity University Mumbai in partial fulfilment of the requirement for the degree of B. Tech Computer Science & Engineering.








Supervisor Name	          Dr. Deepa Parasar









Dr. Shrikant Charhate Director, ASET
 

ACKNOWLEDGEMENT

This project was a great experience of learning and professional development.
We take this opportunity to express our deepest gratitude and special thanks to our mentor,
Dr. Rajiv Iyer, Faculty of the Department of Computer Science and Engineering branch at Amity University, Mumbai. Despite being occupied with their schedule, our mentor took out the time to hear, guide and keep us on the correct path. His constant guidance and support throughout the course of this project have led to its timely and successful completion.

We would also like to express our deepest thanks to our department coordinator Dr. Deepa Parasar for her unwavering support.

We would also like to express our deepest thanks to all the faculty members of Amity School of Engineering and Technology (ASET) for encouraging and motivating us throughout the B. Tech Program.

We would also like to express our deepest thanks to our Director Dr. Shrikant Charhate who has been our pillar of support and motivation throughout the duration of the project. 

Lastly, we would like to express wholehearted thanks to our family members, for supporting us with their love and guidance in whatever we pursue.

We perceive this opportunity as a big milestone in our career development. We shall strive to use the gained skills and knowledge in the best possible way and shall continue to work on their improvement, to attain the desired objectives.
 

ABSTRACT


The dynamic evolution of brain-computer interface (BCI) technology, particularly EEG-based BCIs, is opening new frontiers in interactive applications such as gaming, where users can control and interact with game environments using only their brain activity. This paper explores the design and implementation of an EEG-based BCI system for game control by detecting brainwaves and corresponding cognitive states. Through the development of a custom dataset and the definition of key mental commands, we map brain states—such as focus, relaxation, or motor imagery—to specific in-game actions. These innovations enhance user immersion and inclusivity, especially for individuals with physical limitations. Advancements in machine learning and deep learning have significantly improved the adaptability and classification accuracy of such systems. For instance, a 1-D CNN model achieved 91.75% accuracy in motor imagery classification, while a multi-branch CNN model demonstrated improved cross-subject performance, addressing inter-individual variability in EEG data. Traditional classifiers such as Multi-Layer Perceptron (MLP) and Radial Basis Function (RBF) also continue to perform well, achieving up to 98% classification success, underlining their relevance in EEG-based tasks. Furthermore, recent frameworks aimed at enhancing the interpretability of CNNs offer deeper insight into model decisions, fostering transparency in BCI-driven applications. This research presents a complete end-to-end pipeline—from EEG signal acquisition and brain state labeling to model training and game control execution. The experimental framework demonstrates how real-time mental state recognition can be translated into actionable commands within a gaming environment. Through a series of training sessions and performance evaluations, we validate the feasibility of using thought-driven control schemes, highlighting their accuracy, responsiveness, and potential for further development. The findings contribute to the growing field of non-invasive BCIs and underline the transformative possibilities of integrating cognitive computing with interactive entertainment systems. 

Keywords: deep learning, EEG, cognitive rehabilitation, BCI 

TABLE OF CONTENTS


  CHAPTER NO.	           TITLE				                                                           PAGE NO.	
1.				INTRODUCTION
		1.1		Background
2.				LITERATURE SURVEY
		2.1		Introduction
		2.2		Existing Methodologies		
		2.3		Comparative Analysis
3.        				PROBLEM STATEMENT				
4.				SYSTEM ANALYSIS				
5.				SYSTEM DESIGN	
5.1	Design Model – Use case Diagram/
                          Class Diagram (Detailed Design)
                          Function Specifications (Data flow diagrams)
6.				Comparative Analysis

7.				PROJECT TIMELINE		
		7.1 		Gantt chart		
8.				IMPLEMENTATION, RESULTS AND TESTING	
		8.1		Details of Hardware and Software	
		8.2		Result and Discussion	
9.				CONCLUSION AND FUTURE SCOPE	
		9.1		Conclusion	
		9.2		Future Scope
			             REFERENCES
                                                    PUBLICATIONS AND CERTIFICATES
 
 

LIST OF FIGURES & IMAGES



Sr. No.	Figure/Image Title	Page No.
1	Electrode Placements	28
2	Data Collection 	36
3	Basic Connection for EEG	38
4	DIY Neuroscience Kit	39
5	System Workflow	47
       6 	Data Flow Diagrams	48
7	Gantt Chart	        55        
8	Confusion matrix of XGBoost and Random Forest	        64
9	Confusion matrix for SVM and KNN	       65
10	Graph for beta wave and alpha wave	      66
11	Graph for extended Dataset	      67
12	Label Distribution	     68
13	LSTM Model Accuracy and Loss	    69
       14	Important Features	    69
15	Training loss and Accuracy for XGBoost	   70
16	Training loss and Accuracy	   71
17	Updated Confusion Matrix	   72
18	ROC Curve	   73
19	Precision-Recall curve comparison	   74
 
LIST OF TABLES


Sr. No.	Figure/Image Title	Page No.
1	Comparison of Classification Models	51
2	 Comparison of EEG Devices	54

 

1.	INTRODUCTION


1.1	Background 

Electroencephalography (EEG)--based Brain-Computer Interfaces (BCIs) are redefining how humans interact with digital systems, offering a direct communication pathway between the brain and external devices. Unlike traditional interfaces that rely on muscular activity and tactile input, BCIs tap into the brain’s electrical activity, captured through non-invasive sensors, to interpret user intentions in real-time. This breakthrough enables hands-free interaction, paving the way for novel applications across healthcare, education, entertainment, and assistive technologies. In a world increasingly driven by digital innovation, such technologies offer not just convenience but the possibility of accessibility, inclusivity, and enhanced user experience.

The appeal of BCIs lies in their ability to bridge the gap between cognitive intent and machine response, effectively translating mental activity into executable commands. Within this rapidly advancing field, EEG-based BCIs stand out due to their affordability, portability, and relatively simple setup compared to invasive or semi-invasive alternatives. These systems monitor brainwave patterns—specifically alpha (8–13 Hz), beta (13–30 Hz), theta (4–8 Hz), and delta (0.5–4 Hz) waves—using scalp-mounted electrodes. These waves, associated with various cognitive and physiological states, can be translated into meaningful information when captured accurately and processed appropriately. For instance, alpha waves are typically linked to relaxation, while beta waves are associated with active thinking and focus.

The growing accessibility of consumer-grade EEG devices has accelerated research and development in this space, making it feasible for hobbyists, researchers, and developers to explore cognitive interaction paradigms without requiring high-end laboratory equipment. These systems, when coupled with robust machine learning pipelines and open-source tools, offer unprecedented opportunities to design innovative, real-world applications that can respond to mental commands alone. The ability to harness cognitive states such as attention, relaxation, and motor imagery opens new opportunities for intuitive, seamless interaction with machines, allowing users to engage with digital systems in ways that were once confined to science fiction.

Gaming is emerging as one of the most promising domains for BCI integration. It offers an engaging testbed for real-time neural interaction and a powerful tool for neurocognitive training and rehabilitation. Thought-controlled gaming systems not only revolutionize the gaming experience for able-bodied users but also hold tremendous potential for individuals with physical disabilities. By enabling users to control gameplay using mental commands, BCI-based gaming platforms create a level playing field, eliminating the need for conventional controllers or physical gestures. This shift contributes to greater digital inclusivity, enabling users with limited motor function to access entertainment, cognitive training, and social interaction within virtual environments.
Furthermore, BCI-integrated games provide opportunities for monitoring and improving cognitive performance through neurofeedback mechanisms. Real-time insights into a user’s brain activity can be used to adapt game difficulty dynamically, track focus levels, or even aid in rehabilitation for conditions such as attention-deficit disorders or stroke recovery. This dual-purpose nature—combining entertainment with therapeutic value—marks a unique convergence of neuroscience, artificial intelligence, and human-computer interaction.

However, the transition from theoretical models to real-world BCI applications comes with significant challenges. EEG signals are inherently low-amplitude, noisy, and highly subject-specific. Signal quality may vary with environmental factors, electrode placement, and even the user’s physiological condition. These factors necessitate sophisticated preprocessing and feature extraction techniques to achieve meaningful classification. Filtering techniques such as notch filters (to remove power line interference) and bandpass filters (to isolate specific frequency bands) are crucial in refining raw EEG data into usable signals. Furthermore, real-time applications demand high-speed, accurate inference systems that can adapt to varying user-profiles and environmental conditions.

Another key challenge lies in the variability of EEG signals across individuals and sessions. A model trained on one individual’s brain signals may not generalize well to another user without substantial retraining or transfer learning. Similarly, even the same user may exhibit slightly different EEG patterns based on factors like fatigue, mood, or concentration levels. These inconsistencies pose limitations on model accuracy and long-term reliability, making personalization and adaptive learning essential components of any practical BCI implementation.

Addressing these issues requires a balance between hardware efficiency, signal fidelity, and robust software pipelines. Lightweight models that can run on consumer-grade computers while still achieving high accuracy are essential for real-time applications. Moreover, modular system architectures that allow for easy integration, debugging, and extension make BCI systems more scalable and maintainable.

This research presents the design and development of a cost-effective EEG-based BCI system for controlling a computer game using cognitive states. Utilizing a custom dataset collected through a DIY Neuroscience Kit, we mapped two distinct mental states—attentive and relaxed—to in-game control actions (acceleration and braking). The system leverages open-source tools, Arduino-based data acquisition, and signal processing pipelines involving notch and bandpass filtering. Key features, including Power Spectral Density (PSD) and statistical measures (mean, standard deviation, skewness, and kurtosis), were extracted and fed into machine learning classifiers like XGBoost to predict cognitive states in real time.

To enhance model performance, we incorporated automated hyperparameter tuning using Optuna—a powerful optimization framework that outperforms traditional grid search or random search by efficiently navigating the parameter space. This resulted in increased model accuracy, better generalization across different user sessions, and reduced training time. The choice of XGBoost was influenced by its robustness to overfitting, scalability, and strong performance on tabular data with heterogeneous features.
Real-time implementation was achieved using a lightweight Python-based framework that converts EEG predictions into game control inputs using the pyautogui library. This allowed for seamless interaction between the BCI output and game environment without the need for complex game engine modifications. Additionally, we employed visualization tools to monitor EEG signal quality, classifier confidence levels, and game state synchronization—providing a transparent view into the decision-making process of the system.

The final system demonstrated robust performance in distinguishing between attentive and relaxed mental states, achieving a high classification accuracy and real-time responsiveness suitable for gameplay. By integrating EEG-based BCI with interactive gaming, this study contributes to the expanding frontier of non-invasive neurotechnology. It underscores the potential of combining cognitive computing, real-time analytics, and user-centric design to foster inclusive digital ecosystems.
The broader vision of this project is to promote BCI adoption in mainstream applications, particularly for users who are otherwise limited by conventional control systems. As the technology matures, its applications could extend to smart home systems, augmented communication tools, and immersive VR environments. The goal is not only to demonstrate technical feasibility but also to inspire further innovation in creating adaptive, scalable, and accessible BCI solutions for everyday use. Through this work, we aim to catalyse further research in affordable, adaptive, and user-friendly BCI systems—laying the groundwork for a more inclusive digital future where thoughts can truly control machines.














2.	LITERATURE SURVEY

2.1	 Introduction


Electroencephalography (EEG)--based Brain-Computer Interfaces (BCIs) represent a transformative intersection of neuroscience, engineering, and artificial intelligence, enabling direct communication between the human brain and external devices. This technology, which decodes brain signals to control devices, has widespread application in diverse domains such as neurorehabilitation, assistive technology, human-computer interaction, and secure authentication. EEG-based BCIs are particularly beneficial for individuals with severe motor impairments, such as stroke victims or individuals with paralysis, enabling them to perform tasks through motor imagery (MI) and communicate using brain activity alone (1)

BCI systems typically rely on non-invasive EEG to capture electrical activity in the brain. However, these signals come with inherent challenges such as noise interference, individual differences in brain activity, and the difficulty in translating raw EEG data into meaningful commands. To address these challenges, significant advancements in signal processing, feature extraction, and machine learning techniques have been made. In particular, convolutional neural networks (CNNs) and other deep learning models have demonstrated impressive success in enhancing the accuracy and reliability of EEG-based BCIs [2].

One such advancement is explored in the paper "Authentication with a One-Dimensional CNN Model Using EEG-Based Brain-Computer Interface," which focuses on using EEG signals for user authentication. By employing a 1-D CNN model, the authors achieved an accuracy of 91.75% in classifying motor imagery signals. This approach not only improves classification performance but also introduces a novel method of using EEG-based authentication systems, making it more accessible and secure for individuals, including those with disabilities [3] .In line with this, EEG-based BCIs are being used in a range of applications, including control of external devices such as a mouse cursor through motor imagery. The paper "EEG-based Mouse Cursor Control Using Motor Imagery Brain-Computer Interface" outlines a semi-online BCI system that achieved 93.60% accuracy using an Emotiv EPOC+ headset. The authors integrate real-time data processing with custom algorithms for EEG signal classification, making it an effective solution for assisting users with mobility impairments [4] .

While multi-channel EEG systems have been the standard in EEG-based BCIs, recent studies demonstrate the potential of single-channel EEG systems in making these technologies more affordable and portable. The paper "Motor Imagery Classification Using Single Channel of EEG in Online Brain-Computer Interface" explores this approach, achieving 100% accuracy for left-hand motor imagery and 87.47% accuracy for right-hand motor imagery. By employing wavelet transform for feature extraction and classifiers like SVM and KNN, the study highlights that even single-channel systems can perform competitively, offering a more accessible and cost-effective alternative for real-world applications [5] . Additionally, research has focused on improving cross-subject classification of motor imagery signals, as seen in "Enhancing Cross-Subject Motor Imagery Classification in EEG-Based Brain-Computer Interfaces by Using Multi-Branch CNN." The authors propose a multi-branch CNN model that outperforms previous models by learning generalized features from multiple subjects, thus improving the reliability of MI-based systems across diverse users [6] .

Despite the promising advancements, one of the ongoing challenges is the interpretability of deep learning models used in BCIs. CNNs, while effective, often operate as "black boxes," making it difficult to underst